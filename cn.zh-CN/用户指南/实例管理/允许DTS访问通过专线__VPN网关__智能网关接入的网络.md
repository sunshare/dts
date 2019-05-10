# 允许DTS访问通过专线/VPN网关/智能网关接入的网络 {#concept_227432 .concept}

在配置数据迁移/同步时，选择通过专线/VPN网关/智能网关接入的自建数据库时，需要放通DTS对该网络的访问。

关于如何选择需要放通的IP地址段，请参考[DTS IP地址段](https://help.aliyun.com/document_detail/84900.html)。

## 放通DTS访问通过专线接入的网络 {#section_fjd_lsm_qhb .section}

1.  登录[高速通道管理控制台](https://expressconnect.console.aliyun.com/?spm=a2c4g.11186623.2.13.440959aappcfCu#/vbr/cn-qingdao/list)。
2.  在左侧导航栏，单击**物理专线连接** \> **边界路由器**。
3.  选择边界路由器的地域，然后单击目标边界路由器ID。
4.  单击**路由器条目**，然后单击**添加路由条目**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/190285/155747084646180_zh-CN.png)

5.  配置路由条目，然后单击**确定**。

    |配置|说明|
    |:-|:-|
    |**目标网段**|填入待放通的DTS IP地址段。|
    |**下一跳类型**|选择为**专有网络**。 将目标网段的流量转发至选择的VPC。

 |
    |**下一跳**|选择接收流量的下一跳实例。|

6.  将DTS的IP地址段作为**宣告BGP网段**，详情请参考[宣告BGP网段](https://help.aliyun.com/document_detail/91267.html#h2-url-5)。

## 放通DTS访问通过VPN网关接入的网络 {#section_tnx_wtm_qhb .section}

1.  登录[专有网络控制台](https://vpc.console.aliyun.com)。
2.  在左侧导航栏单击**VPN** \> **IPsec连接**。
3.  编辑IPsec连接的配置信息。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/190285/155747084646184_zh-CN.png)

    **说明：** 在本端网段参数中追加DTS的IP地址段并修改VPN连接版本为**ikev2**。

4.  下载新的VPN配置并修改本地网关设备加载的VPN配置，详情请参考[在本地网关设备中加载VPN配置](https://help.aliyun.com/document_detail/65072.html#h2-url-5)。

