<template>
  <div class="home">
        <div class="form-control">
            <label for="speed" class="name">跑馬燈速度</label>
            <input type="text" id="speed" class="speed" v-model="speed" placeholder="請輸入數字">
        </div>
    <Marquee :speed="speed * 1 || 100" :direction="direction" :data="newsData"/>
  </div>
</template>

<script>
import Marquee from '@/components/marquee';

export default {
    name: 'home',
    components: {
        Marquee,
    },
    data() {
        return {
            speed: '',
            direction: 'Left',
            newsData: [
                {
                    content: '高市雙十國慶升旗萬旗飄揚 近年來首見。',
                },
                {
                    content: 'Climate activist climbs on top of plane during protests at London airport',
                },
                {
                    content: '從NBA火箭隊主管發言風波 看賺中國財最大風險!',
                },
                {
                    content: 'Lorem ipsum dolor sit amet, consetetur sadipscing elitr, sed diam nonumy eirmod tempor invidunt ut labore et dolore magna aliquyam erat, sed diam voluptua.',
                },
                {
                    content: '驚！江蘇高架路大面積坍塌 多車遭斷落路面壓毀。',
                },
                {
                    content: '淨化空氣、涼風、暖風三合一，7折現省7,012元，加送濾網1個(市值$3,000元)，最多省10,012元，免運、立即下單！',
                },
                {
                    content: 'The Turkish President said he would send 3.6 million refugees to Europe if the international community criticizes his military operation',
                },
            ],
        };
    },
};
</script>

<style lang="scss" scoped>
.form-control {
    margin-bottom: 1rem;
}

.name {
    margin-right: 1rem;
}

.speed {
    padding: 0.375rem 0.75rem;
    line-height: 1.5;
    font-size: 1rem;
    color: #495057;
    border: 1px solid #CED4DA;
    border-radius: 0.25rem;
}
</style>
