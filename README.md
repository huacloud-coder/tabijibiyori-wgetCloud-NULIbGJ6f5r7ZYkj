
随着智能手机和其他移动设备的普及，用户对基于位置获取个性化服务的需求大幅增加，例如用户进入商圈范围并停留操作一段时间后，智能触发向用户推送该商圈吃、喝、玩、乐的优惠活动消息；又如当用户到达非常驻地的机场时触发围栏，向用户推送询问是否需要预定机场附近的酒店的消息，从而第一时间洞悉用户潜在需求并提供相关服务。


目前市面上大多数第三方消息推送服务商，通常无法将推送范围精确到某个商圈或较小的区域。而HarmonyOS SDK[位置服务](https://github.com "位置服务")（Location Kit）的[地理围栏技术](https://github.com "地理围栏技术")可以很好地弥补这一不足。


地理围栏就是用一个虚拟的栅栏围出一个虚拟地理边界，当手机进入、离开或在这个围起来的特定地理区域内活动时，手机可以自动接收通知和警告消息。将地理围栏和消息推送相结合，即可实现对特定区域人群的精准消息推送。
![image](https://img2024.cnblogs.com/blog/2396482/202409/2396482-20240903110159306-1685217578.jpg)


比如，当目标受众走进某个商圈附近时，一款生活服务类App将会向他们推送"商圈内多家餐厅正在打折,点击查看优惠详情"的消息，这样可以及时向用户推送与他们当前位置和需求相关的信息,，增加用户的购买意愿。地理围栏技术可以帮助App更精确地定位目标用户，提高推送的精准度和效果。


地理围栏技术也提供了更精确的目标定位能力。比如，在安全监管方面，可以设置围栏以设置特定区域内的活动，提升安全监控和应急响应。在行业场景中，针对物流运输行业，可用于跟踪货物在运输过程中的位置，提醒或者通知相关人员货物的抵达或者离开；随着智能城市和物联网技术的兴起，地理围栏技术也被广泛应用于城市管理、智能交通、环境监测等领域。


![image](https://img2024.cnblogs.com/blog/2396482/202409/2396482-20240903110213571-278550176.png)


### 功能优势


**系统级围栏，围栏功耗低**


**App休眠仍能直接唤醒，围栏检测进程常驻，避免受后台应用影响**


**围栏范围：支持半径200米到10公里范围**


**自定义围栏检测**


**围栏数量不受限制，且可以直接选择商圈围栏**


### 开发步骤


1\.使用地理围栏功能，需要有权限ohos.permission.APPROXIMATELY\_LOCATION，位置权限申请的方法和步骤见[申请位置权限开发指导](https://github.com "申请位置权限开发指导"):[wgetCloud机场](https://tabijibiyori.org)。


2\.导入geoLocationManager模块、wantAgent模块和BusinessError模块



```
import { geoLocationManager } from '@kit.LocationKit';
import { wantAgent } from '@kit.AbilityKit';
import { BusinessError } from '@kit.BasicServicesKit'

```

3\.创建WantAgentInfo信息。


场景一：创建拉起Ability的WantAgentInfo信息。



```
let wantAgentObj:_wantAgent|null = null; // 用于保存创建成功的wantAgent对象，后续使用其完成触发的动作。

// 通过WantAgentInfo的operationType设置动作类型
let wantAgentInfo:wantAgent.WantAgentInfo = {
    wants: [
        {
            deviceId: '',
            bundleName: 'com.example.myapplication',
            abilityName: 'EntryAbility',
            action: '',
            entities: [],
            uri: '',
            parameters: {}
        }
    ],
    operationType: wantAgent.OperationType.START_ABILITY,
    requestCode: 0,
    wantAgentFlags:[wantAgent.WantAgentFlags.CONSTANT_FLAG]
};

```

场景二：创建发布公共事件的WantAgentInfo信息。



```
let wantAgentObj:_wantAgent|null = null; // 用于保存创建成功的WantAgent对象，后续使用其完成触发的动作。

// 通过WantAgentInfo的operationType设置动作类型
let wantAgentInfo:wantAgent.WantAgentInfo = {
    wants: [
        {
            action: 'event_name', // 设置事件名
            parameters: {},
        }
    ],
    operationType: wantAgent.OperationType.SEND_COMMON_EVENT,
    requestCode: 0,
    wantAgentFlags: [wantAgent.WantAgentFlags.CONSTANT_FLAG],
}

```

4\.调用getWantAgent()方法进行创建WantAgent。


并且在获取到WantAgent对象之后调用地理围栏接口添加围栏，当设备进入或者退出该围栏时，系统会自动触发WantAgent的动作。



```
// 创建WantAgent
wantAgent.getWantAgent(wantAgentInfo, (err, data) => {
    if (err) {
      console.error('getWantAgent err=' + JSON.stringify(err));
      return;
    }
    console.info('getWantAgent success');
    wantAgentObj = data;
    let requestInfo:geoLocationManager.GeofenceRequest = {'scenario': 0x301, "geofence": {"latitude": 31.12, "longitude": 121.11, "radius": 100, "expiration": 10000}};
    try {
        geoLocationManager.on('gnssFenceStatusChange', requestInfo, wantAgentObj);
    } catch (err) {
        console.error("errCode:" + JSON.stringify(err));
    }
});

```

注意：目前仅支持圆形围栏，并且依赖GNSS芯片的地理围栏功能，仅在室外开阔区域才能准确识别用户进出围栏事件。


综上所述，地理围栏技术在智能化和定位服务领域具有重要应用。对于开发者来说不仅是提升服务精准度和用户体验的利器，还可以优化资源管理，并创造新的商业价值和解决方案。


**了解更多详情\>\>**


访问[位置服务联盟官网](https://github.com "位置服务联盟官网")


获取[位置服务开发指导文档](https://github.com "位置服务开发指导文档")


