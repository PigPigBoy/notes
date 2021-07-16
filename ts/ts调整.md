# 目前的结构

## ts-lecture

- 活动的一部分逻辑【移动到 ts-operation】
- 课堂活动【保留】
- 生成id【保留】
- 教材 【保留】
- document【保留】
- 资源下载记录【保留】
- 备课一些限制的验证【保留】
- 协同备课【保留】
- 领取资源【保留】（待确认）
- 用户角色权限（wxt、ejt这些）【移到 ts-platform】
- office【移到 ts-platform 或者 its-platform】
- 统计相关【移动到 ts-statistics】
- 会员相关【移动到 ts-operation】
- 申请试用【移动到 ts-operation】
- 用户信息相关 【移到ts-platform】
- 支付、订单相关【移到ts-platform】

## ts-platform
- ts-platform 尽量直接调用apihub
- 配置相关，比如@Async【保留】
- 活动【移动到 ts-operation】
- 专辑【移动到 ts-lecture】
- banner【移动到 ts-operation】
- 机构定制（天力泰）【移动到 ts-operation】
- 帮助中心【移动到 ts-operation】
- 反馈【移动到 ts-operation】
- 消息中心【移动到 ts-operation】
- 卡券【移动到 ts-operation】
- 用户相关【保留】（现有的user接口重量，95%情况仅仅需要userId、名字、头像、机构id，结构化只有在协同备课用到，最好有userApi接口多个实现，根据我参数控制，走不同的实现逻辑）
- 短信nss【保留】
- 微信【保留】

## ts-operation
- 新增的一个运营相关微服务

## ts-statistics
- 新增的一个统计相关微服务
	
## ts-cmmmons
- 已经删除了

## 其他
其中比较费时间的是：
- 1、活动卡券的调整，之前活动拆成了两部分，ts-lecture和ts-platform都有，包括具体逻辑与校验部分。这里需要合并
- 2、此次调整可以屏蔽掉双十一、开学季活动相关代码

# 结构调整后

## ts-lecture

- 专辑
- 课堂活动
- 生成id
- 教材
- document
- 资源下载记录
- 备课一些限制的验证
- 协同备课
- 领取资源

## ts-platform
- 配置相关
- 用户信息相关(包括用户权限、用户信息)
- 支付、订单相关
- office
- 短信nss
- 微信

## ts-operation
- 活动
- 卡券
- 会员
- 申请试用
- 反馈
- 消息中心
- 帮助中心
- banner
- 机构定制（天力泰）

## ts-statistics
- 统计相关


## 预览
![](
http://ts-dev.oss-cn-beijing.aliyuncs.com/test/resource/a.jpg
)

<hr />
<hr />
<hr />

![](
http://ts-dev.oss-cn-beijing.aliyuncs.com/test/resource/b.jpg
)


