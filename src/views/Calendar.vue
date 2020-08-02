<template>
  <div>
    <div class="calendar">
      <el-row class="calendar-header">
        <el-col :span="6">
          <el-button
            type="text"
            icon="el-icon-arrow-left"
            @click="adjustMonth(-1)"
          >
            {{ new Date(calendar.year, calendar.month - 1).getMonth() + 1 }}月
          </el-button>
        </el-col>
        <el-col :span="12">
          <div class="calendar-title">
            <h2>{{ calendar.year }}年 {{ calendar.month + 1 }}月</h2>
          </div>
        </el-col>
        <el-col :span="6">
          <el-button type="text" @click="adjustMonth(1)">
            {{ new Date(calendar.year, calendar.month + 1).getMonth() + 1 }}月
            <i class="el-icon-arrow-right"></i>
          </el-button>
        </el-col>
      </el-row>
      <el-row class="calendar-body">
        <el-col class="week">
          <ul>
            <li>週日</li>
            <li>週一</li>
            <li>週二</li>
            <li>週三</li>
            <li>週四</li>
            <li>週五</li>
            <li>週六</li>
          </ul>
        </el-col>
        <el-col class="days" ref="day">
          <ul>
            <li
              v-for="(day, id) of totalDay"
              :key="id"
              :style="{ height: dayHeight + 'px' }"
              :class="{ notCurrentMonth: day.month !== calendar.month }"
              @click="openDialog(day)"
            >
              {{ day.day }}
            </li>
          </ul>
        </el-col>
      </el-row>
    </div>
    <el-dialog
      :title="dayEventTitle"
      :visible.sync="centerDialogVisible"
      center
      fullscreen
    >
      <div class="tool-bar">
        <div class="people">已報名 0 人，共需 0 人</div>
        <div class="search">
          <el-input
            size="small"
            placeholder="请输入内容"
            suffix-icon="el-icon-date"
            v-model="input3"
          >
          </el-input>
        </div>
      </div>
      <div class="user-viewer">
        <div class="user-content">
          <div class="user-box" v-for="(i, id) of 20" :key="id">
            <img src="/images/img.jpg" alt="" srcset="" />
          </div>
        </div>
      </div>
    </el-dialog>
  </div>
</template>

<script>
export default {
  data() {
    return {
      month_olympic: [31, 29, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31],
      month_normal: [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31],
      month_name: [
        "1月",
        "2月",
        "3月",
        "4月",
        "5月",
        "6月",
        "7月",
        "8月",
        "9月",
        "10月",
        "11月",
        "12月"
      ],
      today: {
        year: 0,
        month: 0,
        day: 0
      },
      calendar: {
        year: 0,
        month: 0,
        day: 0
      },
      dayHeight: 0,
      centerDialogVisible: false,
      dayEvent: {
        title: {}
      }
    };
  },
  mounted() {
    let vm = this;
    this.dateHandler();
    this.resizeFun();
    window.addEventListener("resize", vm.debounce(vm.resizeFun, 100));
  },
  computed: {
    dayEventTitle() {
      return `${this.dayEvent.year} 年 ${this.dayEvent.month + 1} 月 ${
        this.dayEvent.day
      }`;
    },
    monthStart() {
      let date = new Date(
        this.calendar.year,
        this.calendar.month,
        1 - this.getWeek(this.calendar.year, this.calendar.month)
      );
      return date;
    },
    totalDay() {
      let allDate = [];
      for (let i = 0; i < 42; i++) {
        let date = new Date(
          this.monthStart.getFullYear(),
          this.monthStart.getMonth(),
          this.monthStart.getDate() + i
        );
        allDate.push({
          year: date.getFullYear(),
          month: date.getMonth(),
          day: date.getDate()
        });
      }
      return allDate;
    }
  },
  methods: {
    openDialog(day) {
      this.dayEvent = day;
      this.centerDialogVisible = !this.centerDialogVisible;
    },
    adjustMonth(fix) {
      let month = this.today.month + fix;
      if (month < 0) {
        this.calendar.year -= 1;
        this.calendar.month = 11;
      } else if (month > 11) {
        this.calendar.year += 1;
        this.calendar.month = 0;
      } else {
        this.calendar.month += fix;
      }
    },
    debounce(fn, delay) {
      let timer = null;
      return () => {
        var args = arguments;
        var that = this;
        clearTimeout(timer);
        timer = setTimeout(function() {
          fn.apply(that, args);
        }, delay);
      };
    },
    resizeFun() {
      let day = this.$refs.day.$el;
      console.log(day);
      this.dayHeight = Math.floor(day.clientWidth / 7);
    },
    getWeek(year, month) {
      return new Date(year, month, 1).getDay();
    },
    getDays(year, month) {
      let yearCalc = year % 4;
      if (yearCalc == 0) {
        return this.month_olympic[month];
      } else {
        return this.month_normal[month];
      }
    },
    dateHandler() {
      let date = new Date();
      let year = date.getFullYear();
      let month = date.getMonth();
      let day = date.getDate();
      this.today = this.calendar = {
        year,
        month,
        day
      };
    }
  }
};
</script>

<style lang="scss" scoped>
.calendar {
  .calendar-header {
    .calendar-title {
      h2 {
        margin: {
          top: 0;
          bottom: 0;
        }
      }
    }
    * {
      color: black;
    }
  }
  .calendar-body {
    .notCurrentMonth {
      background-color: #eee;
    }
    .week {
      ul {
        display: flex;
        list-style: none;
        padding: 0;
        justify-content: space-between;
        li {
          flex: 1;
        }
      }
    }
    .days {
      ul {
        display: flex;
        list-style: none;
        padding: 0;
        justify-content: flex-start;
        flex-wrap: wrap;
        li {
          flex: calc(100% / 7) 0 0;
          cursor: pointer;
        }
      }
    }
  }
}
.tool-bar {
  display: flex;
  align-items: center;
  padding: 10px 0;
  border-bottom: 1px solid #000;
  .people {
    margin-right: auto;
  }
}
.user-content {
  display: flex;
  flex-wrap: wrap;
  .user-box {
    flex: 0 0 25%;
    max-width: 25%;
    img {
      max-width: 100%;
    }
  }
}
</style>
