<template>
  <div id="rili" v-click-show>
    <!-- 输入框部分 -->
    <div class="input">
      <div class="rili icon">&#xeb99;</div>
      <input type="text" :value="selectDate" />
    </div>

    <!-- 日期选择部分 -->
    <div class="select" v-if="selectShow">
      <!-- 年月 -->
      <div class="year">
        <span class="icon" @click="changeYear('prv')">&#xe714;</span>
        <span class="icon singo" @click="changeMonth('prv')">&#xe712;</span>
        <span class="text">{{ selectShowDate.year }}年{{ selectShowDate.month + 1 }}月</span>
        <span class="icon singo" @click="changeMonth('next')">&#xe718;</span>
        <span class="icon" @click="changeYear('next')">&#xe713;</span>
      </div>
      <!-- 星期 -->
      <div class="week">
        <ul>
          <li
            v-for="item in ['日', '一', '二', '三', '四', '五', '六']"
            :key="item"
          >
            {{ item }}
          </li>
        </ul>
      </div>
      <div class="day">
        <ul>
          <li
            :class="{
              'is-no': !isCur(item).month,
              'is-yes': isCur(item).select,
              'is-day': isCur(item).today,
            }"
            v-for="item in showDay"
            :key="item.getTime()"
            @click="changeSelectDate(item)"
          >
            {{ item.getDate() }}
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    date: {
      type: Date,
      default() {
        return new Date();
      },
    },
  },
  data() {
    return {
      selectShow: false,
      selectShowDate: {
        year: 0,
        month: 0,
        day: 0,
      },
    };
  },
  directives: {
    "click-show": {
      bind(el, binding, vnode) {
        const vm = vnode.context; //绑定的dom元素

        //添加点击事件 判断是否要隐藏选择区
        window.onclick = function (e) {
          const dom = e.target;
          const isHas = el.contains(dom);
          if (isHas && !vm.selectShow) {
            vm.selectShow = true;
          } else if (!isHas && vm.selectShow) {
            vm.selectShow = false;
          }
        };
      },
    },
  },
  computed: {
    // 获取传入的日期展示在input
    selectDate() {
      const { year, month, day } = this.getYearMonthDay(this.date);
      return `${year}-${month + 1}-${day}`;
    },

    // 获取当月日历显示日期
    showDay() {
      const arr = [];
      const { year, month } = this.selectShowDate;
      const firstDate = new Date(year, month, 1);
      const week = firstDate.getDay();
      const startDate = firstDate - week * 1000 * 60 * 60 * 24;

      for (let i = 0; i < 42; i++) {
        arr.push(new Date(startDate + i * 24 * 60 * 60 * 1000));
      }
      return arr;
    },
  },
  methods: {
    // 获取日期对象的年月日
    getYearMonthDay(date) {
      const year = date.getFullYear();
      const month = date.getMonth();
      const day = date.getDate();
      return {
        year,
        month,
        day,
      };
    },

    //重置selectShowDate信息
    updateShowDate() {
      const { year, month, day } = this.getYearMonthDay(this.date);
      this.selectShowDate = {
        year,
        month,
        day,
      };
    },

    // 判断日期是上个月还是本月还是今天，添加class
    isCur(date) {
      //日历显示时间
      const { year, month, day } = this.getYearMonthDay(date);
      //当前时间
      const {year: nowYear,month: nowMonth,ay: nowDay,} = this.getYearMonthDay(new Date());
      //当前选中时间
      const {year: selectYear,month: selectMonth,day: selectDay,} = this.getYearMonthDay(this.date);
      return {
        month: year === nowYear && month === nowMonth,
        select:
          year === selectYear && month === selectMonth && day === selectDay,
        today: year === nowYear && month === nowMonth && day === nowDay,
      };
    },

    // 改变select区年份
    changeYear(res) {
      if (res === "prv") {
        this.selectShowDate.year--;
      } else {
        this.selectShowDate.year++;
      }
    },

    //改变select区月份
    changeMonth(res) {
      const {year , month , day} = this.selectShowDate
      const moveMonth = res === 'prv' ? -1 : 1
      let showDay = new Date(year,month,day)
      showDay.setMonth(month + moveMonth)
      const {year:newYear,month:newMonth} = this.getYearMonthDay(showDay)
      this.selectShowDate.year = newYear
      this.selectShowDate.month = newMonth
    },

    // 改变选中日期
    changeSelectDate(date) {
      this.$emit('changeSelectDate',date)
      this.selectShow = false
      const {year,month,day} = this.getYearMonthDay(date)
      this.selectShowDate = {
        year,
        month,
        day
      }
    }
  },
  created() {
    this.updateShowDate();
  },
};
</script>


<style scoped>
@import url("./font-face.css");

#rili {
  display: inline-block;
}

#rili .input {
  position: relative;
}
#rili .input input {
  width: 200px;
  height: 40px;
  box-sizing: border-box;
  padding: 5px 0 5px 40px;
  border-radius: 7px;
  outline: none;
  border: 1px solid #dcdfe6;
  cursor: pointer;
}
#rili .input .rili {
  position: absolute;
  color: #c0c4cc;
  left: 10px;
  font-size: 1.5rem;
  width: 30px;
  height: 40px;
  line-height: 40px;
}

.select {
  position: absolute;
  margin-top: 7px;
  width: 322px;
  height: 329px;
  border: 1px solid rgba(0, 0, 0, 0.1);
  border-radius: 5px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
  user-select: none;
}

.select .year {
  width: 100%;
  height: 40px;
  display: flex;
  justify-content: center;
  align-items: center;
}
.select .year .text {
  margin: 0 30px;
}
.select .year .icon {
  font-size: 0.8rem;
  cursor: pointer;
}

.select .week ul {
  display: flex;
  justify-content: space-around;
  margin: 5px 10px;
  padding-bottom: 10px;
  border-bottom: 1px solid #ebeef5;
  font-size: 0.8rem;
}

.select .day ul {
  display: flex;
  flex-wrap: wrap;
  margin: 0 10px;
  justify-content: space-around;
}

.select .day ul li {
  height: 30px;
  width: 30px;
  text-align: center;
  font-size: 0.8rem;
  box-sizing: border-box;
  line-height: 30px;
  margin: 5px 6px;
  cursor: pointer;
}
.select .day ul li:hover {
  color: #409eff;
}

.select .day ul li.is-day {
  color: #409eff;
  font-weight: 700;
}

.select .day ul li.is-no {
  color: #c0c4cc;
}
.select .day ul li.is-yes {
  color: white;
  border-radius: 50%;
  background-color: #409eff;
}
</style>