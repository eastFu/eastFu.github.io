---
title: zookeeper的断线重连实现
toc: true
tags: 编程
categories:
  - 编程
  - Zookeeper
abbrlink: 32523
date: 2016-06-18 10:31:48
---
## zookeeper简介

ZooKeeper是一个分布式的，开放源码的分布式应用程序协调服务，是Google的Chubby一个开源的实现，是Hadoop和Hbase的重要组件。
我们可以使用zookeeper做程序的健康监测（EPHEMERAL 临时节点）、公共配置文件、集群管理（leader 选举）等等。
但是zookeeper并没有提供断线重连的功能，必须我们手动实现，这里使用 Curator来实现了zookeeper的断线重连功能，代码如下：
```
import java.io.UnsupportedEncodingException;

import org.apache.curator.framework.CuratorFramework;
import org.apache.curator.framework.CuratorFrameworkFactory;
import org.apache.curator.framework.listen.ListenerContainer;
import org.apache.curator.framework.state.ConnectionState;
import org.apache.curator.framework.state.ConnectionStateListener;
import org.apache.curator.retry.ExponentialBackoffRetry;
import org.apache.zookeeper.CreateMode;


/**
 * @ClassName: ZookeeperExcutor
 * @Description: zookeeper连接处理器
 */
public class ZookeeperExcutor {

    private CuratorFramework client;

    public ZookeeperExcutor(String zklist,int sessionTimeout,int connectTimeout){
        client = CuratorFrameworkFactory.builder()
                .connectString(zklist).sessionTimeoutMs(sessionTimeout)
                .connectionTimeoutMs(connectTimeout)
                .retryPolicy(new ExponentialBackoffRetry(1000, 3)).build();
        client.start();
    }

    public CuratorFramework getClient() {
        return client;
    }

    /**
     * @Title: createNodeAddListener
     * @Description: 添加node节点
     * @param nodePath
     * @param nodeData 设定文件
     * @return void    返回类型
     */
    public String createNode(String nodePath,String nodeData){
        if(client!=null){
            try {
                String nodeName=client.create().creatingParentsIfNeeded()
                .withMode(CreateMode.EPHEMERAL_SEQUENTIAL)
                .forPath(nodePath, nodeData.getBytes("UTF-8"));
                return nodeName;
            } catch (UnsupportedEncodingException e) {
                e.printStackTrace();
                return null;
            } catch (Exception e) {
                e.printStackTrace();
                return null;
            }
        }
        return null;
    }
    /**
     * @Title: getListener
     * @Description: 为节点添加 connectState 监听器，实现断线重连，然后添加上节点
     * @param nodePath    节点路径
     * @param nodeData 节点数据
     * @return void    返回类型
     */
    public ConnectionStateListener getListener(final String nodePath,final String nodeData){
        if(null!=client){
            ConnectionStateListener connectListener = new ConnectionStateListener() {
                @Override
                public void stateChanged(CuratorFramework curatorFramework, ConnectionState connectionState) {
                    if (connectionState == ConnectionState.LOST) {
                        while (true) {
                            try {
                                //手动重连
                                boolean flag=curatorFramework.getZookeeperClient().blockUntilConnectedOrTimedOut();
                                if (flag){
                                    //重新添加节点
                                    clearListener();
                                    createNode(nodePath, nodeData);
                                    client.getConnectionStateListenable().addListener(getListener(nodePath, nodeData));
                                    break;
                                }
                            } catch (InterruptedException e) {
                                e.printStackTrace();
                            } catch (Exception e) {
                                e.printStackTrace();
                            }
                        }
                    }else if(connectionState==ConnectionState.RECONNECTED){
                        //重新连接成功
                    }else if(connectionState==ConnectionState.SUSPENDED){
                        //自动重连,自动新建 schedular的临时节点
                    }
                }

            };
            return connectListener;
        }
        return null;
    }

    public void clearListener(){
        ListenerContainer<ConnectionStateListener> list=(ListenerContainer<ConnectionStateListener>) client.getConnectionStateListenable();
        list.clear();
    }

    public void addListener(String nodePath,String nodeData){
        client.getConnectionStateListenable().addListener(getListener(nodePath, nodeData));
    }

    public static void main(String[] args) {
        ZookeeperExcutor zke=new ZookeeperExcutor("127.0.0.1:2181",10000, 10000);
        String nodeName=zke.createNode("/Test", "test");
        if(null!=nodeName){
            zke.addListener("/Test", "test");
        }
    }
}
```
## 关于curator
Curator是Netflix公司开源的一个Zookeeper客户端，与Zookeeper提供的原生客户端相比，Curator的抽象层次更高，简化了Zookeeper客户端的开发量，原生的zookeeper实现起来稍微麻烦一点。
下面是Curator的maven配置：
```
<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-framework</artifactId>
    <version>2.4.2</version>
</dependency>
<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-client</artifactId>
    <version>2.4.2</version>
</dependency>
<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-recipes</artifactId>
    <version>2.4.2</version>
</dependency>
```
