---
id: phoenix-admin-use-2x
title: 使用说明
---

##  项目配置
1. 新建项目
![image-20200115193059777](../../assets/phoenix2.x/phoenix-admin/image-20200115193059777.png)
2. 配置项说明
   ![image-20200115200309243](../../assets/phoenix2.x/phoenix-admin/002.png)
   * 项目ID：
     Phoenix项目的ID，全局唯一，建议使用英文+字母的简短组合。
   * 项目名称：
     Phoenix项目的名称。
   * 项目描述：
     Phoenix项目的简短描述。
   * 注册中心地址：
     phoenix-admin能实现多项目的管理，借住了eureka来实现各个项目内的服务发现。这里需要填写在部署Phoenix项目的时候，eureka的地址。

## 系统配置
1. 添加数据源
   ![image-20200115200504478](../../assets/phoenix2.x/phoenix-admin/004.png)
2. 配置项说明：
   ![image-20200115200709499](../../assets/phoenix2.x/phoenix-admin/002.png)
   * Url
     因为Phoenix项目内的Phoenix服务，是把监控点上报到elasticsearch的，这里我们填写该项目下的elasticsearch的url。
   * Namespace
     填写利用Kubernetes部署项目的时候，Kubernetes的命名空间。

## 效果展示
配置完毕后，点击右侧菜单栏的[Grafana]按钮，即可实现免密跳转到grafana的默认dashboard中。
![image-20200115201142587](../../assets/phoenix2.x/phoenix-admin/image-20200115201142587.png)
效果如下：
![image-20200115201222406](../../assets/phoenix2.x/phoenix-admin/image-20200115201222406.png)
备注：对于上述图表中的各个面板的解释说明，请见上一篇文档。图表中各项指标的含义，在图表中有中文图例说明。