<template>
  <div id="app">
    <PageMaze :maze="mazeData" :mazeIndexTable="mazeIndexTable" />
  </div>
</template>

<script>
import PageMaze from './components/PageMaze.vue'

export default {
  name: 'app',
  components: {
    PageMaze
  },
  data() {
    return {
      mazeData: {
        0: {
          x: 1,
          y: 0,
          title: '妙蛙種子',
          cover: require('@/assets/pokemon/1.png'),
          description: '經常可見牠在太陽下睡午覺的樣子。在沐浴了充足的陽光之後，牠背上的種子就會成長茁壯。',
          url: '',
          data: '',
        },
        1: {
          x: 1,
          y: 1,
          title: '妙蛙草',
          cover: require('@/assets/pokemon/2.png'),
          description: '下盤為了支撐背上的花苞而變得強韌。當待在太陽底下一動也不動的時間變長，就表示大朵的花即將盛開。',
          url: '',
          data: '',
        },
        2: {
          x: 1,
          y: 2,
          title: '妙蛙花',
          cover: require('@/assets/pokemon/3.png'),
          description: '據說充足的營養和陽光會讓花朵的顏色變得更加鮮豔。花朵散發的香氣能夠療癒人心。',
          url: '',
          data: '',
        },
        3: {
          x: 2,
          y: 2,
          title: '小火龍',
          cover: require('@/assets/pokemon/4.png'),
          description: '尾巴上的火焰代表牠的心情。開心時火焰會搖曳晃動，生氣時火焰則會猛烈燃燒。',
          url: '',
          data: '',
        },
        4: {
          x: 3,
          y: 2,
          title: '火恐龍',
          cover: require('@/assets/pokemon/5.png'),
          description: '會用銳利的爪子無情地打倒對手。遇到強敵時會興奮起來，尾巴上會燃起藍白色的火焰。',
          url: '',
          data: '',
        },
        5: {
          x: 3,
          y: 3,
          title: '噴火龍',
          cover: require('@/assets/pokemon/6.png'),
          description: '會在空中四處飛翔尋找強大的對手。在面對比自己弱小的對手時，牠不會吐出那可熔化一切的高溫火焰。',
          url: '',
          data: '',
        },
        6: {
          x: 2,
          y: 1,
          title: '傑尼龜',
          cover: require('@/assets/pokemon/7.png'),
          description: '甲殼的作用不僅是用來保護自己，圓潤的外形和表面的溝槽會減少水的阻力，使牠能夠快速地游泳。',
          url: '',
          data: '',
        },
        7: {
          x: 3,
          y: 1,
          title: '卡咪龜',
          cover: require('@/assets/pokemon/8.png'),
          description: '長滿蓬鬆毛髮的大尾巴，顏色會隨著年齡增長而變深。甲殼上的傷痕是強者的證明。',
          url: '',
          data: '',
        },
        8: {
          x: 4,
          y: 1,
          title: '水箭龜',
          cover: require('@/assets/pokemon/9.png'),
          description: '甲殼上的噴射口能夠精確地瞄準目標。射出的水彈可以命中５０公尺外的空罐子。',
          url: '',
          data: '',
        },
      },
      mazeIndexTable: [
        [1, 0],
        [1, 1],
        [1, 2],
        [2, 2],
        [3, 2],
        [3, 3],
        [2, 1],
        [3, 1],
        [4, 1],
      ],
    };
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
</style>
