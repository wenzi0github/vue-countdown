<template>
    <span>{{countTime}}</span>
</template>
<script>
export default {
    name: 'i-countdown',
    props: {
        diff: {
            type: Number,
            default: 1000
        },
        format: { // 格式化
            type: String,
            default: 'dd hh:mm:ss'
        },
        startTime: { // 开始时间
            type: Number | String
        },
        endTime: { // 结束时间
            type: Number | String,
            required: true
        },
        start: {
            type: Boolean,
            default: true
        },
        stop: {
            type: Boolean,
            default: false
        }
    },

    data () {
        return {
            countTime: '0'
        };
    },

    watch: {
        'start': function (val, oldVal) {
            if (val) {
                this.startSteps({
                    diff: this.diff,
                    format: this.format,
                    startTime: this.startTime,
                    endTime: this.endTime,
                    stop: this.stop
                });
            }
        }
    },

    mounted () {
        this.startSteps({
            diff: this.diff,
            format: this.format,
            startTime: this.startTime,
            endTime: this.endTime,
            stop: this.stop
        });
    },

    methods: {
        /**
         * 倒计时
         * @param {*} 配置
         * @param {number} diff 变动的频率，单位是ms
         * @param {string} format string 返回的格式
         * @param {string, number}startTime 开始时间，格式时间或毫秒级时间戳 '2018/08/08 23:59:59' | 1533743999000
         * @param {string, number}endTime 截止时间，格式时间或毫秒级时间戳 '2018/08/08 23:59:59' | 1533743999000
         * @param {boolean} stop 是否直接当前倒计时
         * @param {function} steps 每一步执行的方法，参数为当前的倒计时时间，格式就是format参数里设定的
         * @param {function} finished 倒计时结束执行的方法，参数为当前的倒计时时间，格式就是format参数里设定的
         */
        startSteps ({
            diff = 1000,
            format = 'dd hh:mm:ss',
            startTime = 0,
            endTime = -1,
            steps = () => {},
            finished = () => {}
        } = {}) {
            if (!diff) {
                throw new Error('diff must be upper 10ms \n变动频率diff必须大于10ms');
            }

            let starttime = new Date(typeof startTime === 'string' ? startTime.replace(/-/g, '/') : startTime);
            let endtime = new Date(typeof endTime === 'string' ? endTime.replace(/-/g, '/') : endTime); // iOS中做下兼容处理
            if (!endtime || !endTime) {
                return false;
            }
            let _fresh = () => {
                let now = Date.now(),
                    difs = Math.max(parseInt((endtime.getTime() - now) / 1000), 0);

                if (starttime > now) {
                    // this.$emit('statusChanged', 'pending');
                    return false;
                }

                let s = {
                        'd+': parseInt(difs / 3600 / 24),
                        'h+': parseInt((difs / 3600) % 24),
                        'm+': parseInt((difs / 60) % 60),
                        's+': parseInt(difs % 60)
                    };

                let ss = this.dateFormat(s, format);
                if (difs <= 0 || this.stop) {
                    this.$emit('finished', ss);
                    // this.$emit('statusChanged', 'finished');
                    return false;
                }
                this.countTime = ss;
                this.$emit('steps', ss);
                // this.$emit('statusChanged', 'running');
            };

            _fresh();
            this.timer = setInterval(_fresh, diff);
        },

        // 手动停止计时器
        // stop () {
        //     this.timer && clearInterval(this.timer);
        // },

        dateFormat (timeline, fmt) {
            let o = {
                'd+': '', // 日
                'h+': '', // 小时
                'm+': '', // 分
                's+': '', // 秒
                'q+': '', // 季度
                'S': '' // 毫秒
            };
            Object.assign(o, timeline);

            for (let k in o) {
                if (new RegExp('(' + k + ')').test(fmt)) {
                    fmt = fmt.replace(RegExp.$1, (RegExp.$1.length === 1)
                        ? (o[k])
                        : (('00' + o[k]).substr(('' + o[k]).length)));
                }
            }

            return fmt;
        }
    }
};
</script>
