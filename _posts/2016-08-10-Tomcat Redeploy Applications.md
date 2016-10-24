---
layout: post
title: Tomcat Auto Redeploy Application
tags:Tomcat 
category: Tomcat
comments: true
---


利用Jenkins与Tomcat集成发布release，自动deploye最新的项目运行在Tomcat中，在deploye的过程中会涉及到工程项目的删除，但是Tomcat CMD 仍处于运行状态，导致旧的工程无法删除，无法部署最新的项目至serve中. 这是就需要对tomcat的相关参数进行配置

在${Tomcat_Home}/conf/context.xml 中， 应添加属性antiResourceLocking="true"， antiJARLocking="true" .

>       

        <?xml version="1.0" encoding="UTF-8"?>
        <!-- Licensed to the Apache Software Foundation (ASF) under one or more contributor license agreements. See the NOTICE file distributed with this work for additional information regarding copyright ownership. The ASF licenses this file to You under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. You may obtain a copy of the License at http://www.apache.org/licenses/LICENSE-2.0 Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License. -->
        
        <!-- The contents of this file will be loaded for each web application -->
        -<Context antiJARLocking="true" antiResourceLocking="true"> 
        <!-- Default set of monitored resources -->
         <WatchedResource>WEB-INF/web.xml</WatchedResource> 
        <!-- Uncomment this to disable session persistence across Tomcat restarts -->
         
        <!-- <Manager pathname="" /> -->
         
        <!-- Uncomment this to enable Comet connection tacking (provides events on session expiration as well as webapp lifecycle) -->
         
        <!-- <Valve className="org.apache.catalina.valves.CometConnectionManagerValve" /> -->
         </Context>

