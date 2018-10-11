# vue-countdown
vue2中的倒计时组件

使用方式： 

```vue
<div class="lefttime">
    <i-countdown :endTime="Date.now()+1000000" :diff="1000" :format="'d天hh小时mm分ss秒'"></i-countdown>
</div>
```

```javascript
import iCountdown from '@/ui/i-countdown';

export default {
    components: {
        iCountdown
    },
}
```

`i-countdown`组件中接收的参数有： 

* endTime: {string | number} 截止时间，格式时间或毫秒级时间戳 '2018/08/08 23:59:59' | 1533743999000
* diff: number 倒计时的频率，单位毫秒，默认为1000
* format: string 展示的时间格式， d表示天， h表示时间， m表示分， s表示秒
* start: boolean 手动开启倒计时
* stop: boolean 手动结束倒计时

监听的方法： 

* @steps 每次倒计时触发一次
* @finished 倒计时结束时触发，手动结束倒计时也会触发的
