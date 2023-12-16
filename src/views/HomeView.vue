<template>
  <!-- v-show="shomes" -->
  <div
    v-loading="!shomes"
    element-loading-text="Loading..."
    element-loading-spinner="el-icon-loading"
    element-loading-background="rgba(0, 0, 0, 0.8)"
    style="
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      z-index: 9999;
    "
    ref="DIVOS"
  >
    <div v-show="window.innerWidth < 400" class="onlyPhone">
      אין תמיכה במובייל פתח מחשב!
    </div>
    <div v-show="window.innerWidth > 400">
      <div class="logo"><strong>Js84df</strong>-חברת</div>
      <el-dropdown
        class="dropdown"
        v-if="Department.length > 0 && data2.length > 0"
      >
        <span class="el-dropdown-link">
          נתונים<i class="el-icon-arrow-down el-icon--right"></i>
        </span>
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item
            ><div class="Depart">
              סך הכל מחלקות
              <span
                ><strong> {{ Department.length }}</strong></span
              >
            </div></el-dropdown-item
          >
          <el-dropdown-item>
            <div class="m">סך הכל עובדים {{ data2.length }}</div>
          </el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
      <el-badge value="3" class="icon" type="primary"> </el-badge>
      <i class="el-icon-message-solid"></i>
      <div class="divshelemaala"></div>
      <div class="hazeshebatzad">
        <div @click="hosefOved">
          <el-row class="row">
            <el-col :span="24"><i class="el-icon-user"></i> הוסף עובד</el-col>
          </el-row>
        </div>
        <div @click="haserOved">
          <el-row class="row">
            <el-col :span="24"><i class="el-icon-delete"></i> הסר עובד</el-col>
          </el-row>
        </div>
        <div @click="hosefMahlaka">
          <el-row class="row">
            <el-col :span="24"><i class="el-icon-top"></i> הוסף מחלקה</el-col>
          </el-row>
        </div>
        <div v-if="mehika">
          <el-row>
            <el-col :span="24"
              ><el-button
                type="primary"
                class="buttonOfBack"
                @click="mehika = false"
                >חזור</el-button
              >
            </el-col>
          </el-row>
        </div>
        <div
          @click="
            showN = true;
            showZeYafe = true;
          "
        >
          <el-row class="row">
            <el-col :span="24"
              ><i class="el-icon-s-order"></i> סטטוס מחלקות</el-col
            >
          </el-row>
        </div>
        <div v-for="n in 7" :key="n" @click="showZeYafe = true">
          <el-row class="row">
            <el-col :span="24">{{ `סתם ליופי -${n}` }}</el-col>
          </el-row>
        </div>
      </div>
      <div>
        <el-input
          v-model="serch"
          :placeholder="pleace"
          class="input"
          ref="inputo"
        ></el-input>
        <el-select
          v-model="selcto"
          placeholder="חפש לפי"
          class="selctA"
          ref="selctA"
        >
          <el-option
            v-for="(m, i) in ['שם עובד', 'תפקיד', 'מחלקה']"
            :key="i"
            :value="m"
            :label="m"
          ></el-option>
        </el-select>
        <el-select
          v-model="filterDeparment"
          placeholder="בחר מחלקה"
          class="selectB"
        >
          <el-option
            v-for="(sq, i) in Department"
            :key="i"
            :value="sq"
          ></el-option>
          <el-option value="הכל"></el-option>
        </el-select>
      </div>
      <el-table :data="data" class="table" stripe v-show="data.length > 0">
        <el-table-column label="שם עובד" prop="Name"></el-table-column>
        <el-table-column label="תפקיד" prop="Position"></el-table-column>
        <el-table-column label="מחלקה" prop="DepartmentName"></el-table-column>
        <el-table-column label="מחיקה" v-if="mehika">
          <template slot-scope="scope">
            <el-button
              class="buttono"
              :loading="loadingButton"
              type="danger"
              @click="DeleteUs(scope.row.EmployeeID)"
              size="mini"
              ><i class="el-icon-delete"></i
            ></el-button>
          </template>
        </el-table-column>
      </el-table>
      <div class="hazeshemehase" v-if="mehika"></div>
    </div>

    <div class="ZeYafe" v-if="showZeYafe">
      <transition appear name="expand">
        <div class="inZeYafe">
          <i
            class="el-icon-close"
            @click="
              showZeYafe = false;
              showM = false;
              showN = false;
            "
          ></i>
          <div v-show="NewUser && showZeYafe" class="theNew">
            <div class="alltheinputs">
              <div
                style="text-align: right; font-size: 20px; margin-bottom: 10px"
              >
                שם העובד:
              </div>
              <el-input
                style="width: 200px"
                size="small"
                v-model="ovedHadash.shem"
                placeholder="שם העובד החדש"
                class="ktzatSmall"
              ></el-input>
              <div
                style="text-align: right; font-size: 20px; margin-bottom: 10px"
              >
                שם המחלקה:
              </div>
              <el-select
                size="small"
                v-model="ovedHadash.Department"
                placeholder=""
                class="selecto"
              >
                <el-option
                  v-for="(d, i) in Department"
                  :key="i"
                  :value="d"
                ></el-option>
              </el-select>
              <div
                style="text-align: right; font-size: 20px; margin-bottom: 10px"
              >
                תפקיד:
              </div>
              <el-input
                class="ktzatSmall"
                style="width: 200px"
                v-model="ovedHadash.position"
                placeholder="תפקיד"
                size="small"
              ></el-input>
            </div>
            <div class="pratim">
              <div>
                שם העובד :<span class="ze">{{ ovedHadash.shem }}</span>
              </div>
              <div>
                שם המחלקה :<span class="ze">{{ ovedHadash.Department }}</span>
              </div>
              <div>
                שם התפקיד :<span class="ze">{{ ovedHadash.position }}</span>
              </div>
            </div>
            <div class="butons">
              <el-button
                type="success"
                @click="AddOved"
                :loading="loadingButton"
                >שמור</el-button
              >
              <el-button
                type="danger"
                @click="
                  NewUser = false;
                  showZeYafe = false;
                "
                >צא</el-button
              >
            </div>
          </div>
          <div class="AddMahlaka" v-show="showM && showZeYafe">
            <div style="text-align: right">שם המחלקה החדש:</div>
            <br />
            <el-input
              v-model="shemhadash"
              placeholder="שם המחלקה החדשה"
              style="width: 60%"
              class="inputicatica"
            ></el-input>
            <el-button
              type="success"
              class="butoon"
              @click="hosefOta"
              :loading="loadingButton"
              >הוסף</el-button
            >
            <el-button
              type="danger"
              @click="
                showM = false;
                shemhadash = '';
                showZeYafe = false;
              "
              class="tze"
              >חזור</el-button
            >
            <div class="ze r">{{ shemhadash }}</div>
            <div class="smotDEPARTMENT">
              <div class="smotDEPARTMENTtitle">כל המחלקות</div>
              <div class="depart-item" v-for="(d, i) in Department" :key="i">
                {{ d }}
              </div>
            </div>
          </div>
          <el-table
            :data="netunim"
            class="tableNetunim"
            v-show="netunim.length > 0 && showN && showZeYafe"
          >
            <el-table-column
              label="סך הכל עובדים"
              prop="TOTAL"
            ></el-table-column>
            <el-table-column
              label="שם המחלקה"
              prop="DepartmentName"
            ></el-table-column>
            <el-table-column label="מנהל מחלקה" prop="Manger"></el-table-column>
          </el-table>
        </div>
      </transition>
    </div>
  </div>
</template>
<script>
import { URL } from "@/URL/url";
export default {
  data() {
    return {
      window,
      NewUser: false,
      data: [],
      data2: [],
      serch: "",
      selcto: "שם עובד",
      netunim: [],
      ovedHadash: {
        shem: "",
        Department: "",
        position: "",
      },
      Department: [],
      filterDeparment: "הכל",
      mehika: false,
      loadingButton: false,
      shemhadash: "",
      showM: false,
      showZeYafe: false,
      shomes: false,
      showN: false,
    };
  },
  watch: {
    serch(val) {
      // this.data = this.data2;
      if (this.selcto === "שם עובד") {
        this.data = this.data.filter((e) => {
          return e.Name.includes(val);
        });
      }
      if (this.selcto === "תפקיד") {
        this.data = this.data.filter((e) => {
          return e.Position.includes(val);
        });
      }
      if (this.selcto === "מחלקה") {
        this.data = this.data.filter((e) => {
          return e.DepartmentName.includes(val);
        });
      }
      if (val === "") {
        this.data = this.data2;
        this.filterDeparmentMET(this.filterDeparment);
      }
    },
    filterDeparment(val) {
      this.data = this.data2;
      this.data = this.data.filter((e) => {
        return e.DepartmentName === val;
      });
      if (val === "" || val === "הכל") {
        this.data = this.data2;
      }
    },
    NewUser(val) {
      if (val) {
        this.showM = false;
        this.showN = false;
      }
    },
    showM(val) {
      if (val) {
        this.NewUser = false;
        this.showN = false;
      }
    },
    showN(val) {
      if (val) {
        this.NewUser = false;
        this.showM = false;
      }
    },
  },
  computed: {
    pleace() {
      return `סנן לפי ${this.selcto}...`;
    },
  },
  async mounted() {
    let { data } = await this.$ax(URL);
    // console.log(data);
    this.data = data;
    this.data2 = data;
    let inp = this.$refs.inputo.$el.children[0];
    inp.style.background = "rgba(6, 178, 135, 0.788)";
    inp.style.color = "white";
    // inp.style.width = "384px";
    inp.style.width = "40%";
    inp.style.position = "absolute";
    inp.style.left = "584px";
    let selc = this.$refs.selctA.$el.children[0].children[0];
    selc.style.background = "rgba(255, 238, 225, 0.663)";
    let res = await this.$ax.get(URL + "Getnetunim");
    this.netunim = res.data;
    this.shomes = true;
    this.$refs.DIVOS.style.zIndex = "";

    let D = await this.$ax.get(URL + "GetD");
    this.Department = D.data;
  },

  methods: {
    filterDeparmentMET(val) {
      this.data = this.data2;
      this.data = this.data.filter((e) => {
        return e.DepartmentName === val;
      });
      if (val === "" || val === "הכל") {
        this.data = this.data2;
      }
    },
    hosefOved() {
      this.NewUser = true;
      this.showM = false;
      this.showN = false;
      this.showZeYafe = true;
    },
    async AddOved() {
      if (
        this.ovedHadash.shem &&
        this.ovedHadash.Department &&
        this.ovedHadash.position
      ) {
        this.loadingButton = true;
        let { data } = await this.$ax.post(URL + "AddOved", this.ovedHadash);
        console.log(data);
        if (data) {
          this.$message.success("העובד נוסף בהצלחה");
          this.loadingButton = false;
          window.location.reload();
        } else {
          this.$message.error("משהו נכשל נסה שוב");
        }
      } else {
        this.$message.error("לא מילאת הכל");
      }
      // console.log({
      //   shem: this.ovedHadash.shem,
      //   D: this.ovedHadash.Department,
      //   P: this.ovedHadash.position,
      // });
      // ovedHadash: {
      //   shem: "",
      //   Department: "",
      //   position: "",
      // },
    },
    haserOved() {
      this.mehika = true;
    },
    async DeleteUs(id) {
      console.log(id);
      this.loadingButton = true;
      let { data } = await this.$ax.delete(URL + "deleteUser/" + id);
      if (data) {
        this.$message.success("העובד נמחק");
        let i = this.data.findIndex((e) => e.EmployeeID === id);
        this.data.splice(i, 1);
        this.loadingButton = false;
        this.mehika = false;
      } else {
        this.$message.error("משהו השתבש");
      }
    },
    hosefMahlaka() {
      this.showM = true;
      this.NewUser = false;
      this.showZeYafe = true;
    },
    async hosefOta() {
      // console.log(this.Department.includes(this.shemhadash));
      if (this.shemhadash && !this.Department.includes(this.shemhadash)) {
        this.loadingButton = true;
        const { data } = await this.$ax.post(URL + "AddDepartment", {
          newP: this.shemhadash,
        });

        if (data) {
          this.loadingButton = false;
          this.$message.success("נוספה בהצלחה");

          setTimeout(() => {
            window.location.reload();
          }, 500);
        } else {
          this.$message.error("משהו נכשל");
        }
      } else {
        if (this.Department.includes(this.shemhadash)) {
          this.$message.error("יש כבר מחלקה בשם זה");
        } else {
          this.$message.error("לא מלאת כלום");
        }
      }
    },
  },
};
</script>
<style scoped>
body {
  background: rgba(255, 238, 225, 0.663);
}
.row {
  margin-bottom: 6px;
  text-align: center;
  background: white;
  height: 40px;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 2;
  border-radius: 5px;
}
.row:hover {
  cursor: pointer;
  background: rgb(130, 114, 114);
  color: coral;
  border-radius: 30px;
  font-size: 20px;
}
.hazeshebatzad {
  position: absolute;
  top: 40px;
  right: 0;
  background: rgb(57, 158, 198);
  width: 230px;
  height: 130%;
  padding: 5px;
}
.table {
  width: 84.3%;
  /* width: 30%; */
  position: absolute;
  left: 0;
  top: 81px;
  border: 0.4px solid black;
  padding: 3px;
  height: 88%;
  overflow: auto;
  z-index: 305;
}
.table::-webkit-scrollbar {
  display: none;
}
.input {
  /* width: 1500px; */
  /* width: 70%; */
  /* background: rgb(0, 0, 0); */
  position: absolute;
  left: 97px;
  top: 40px;
  direction: rtl;
  z-index: 201;
}
.buttonOfBack {
  z-index: 302;
  width: 100%;
  position: absolute;
}
.buttono {
  z-index: 302;
  position: relative;
}
.selctA {
  position: absolute;
  top: 40px;
  left: 37%;
  width: 113px;
  z-index: 201;
}
.selectB {
  position: absolute;
  top: 40px;
  width: 37%;
}
.tableNetunim {
  width: 100%;
  height: 100%;
  position: absolute;
  left: 0;
  top: 0;
}
.theNew {
  width: 580px;
  /* width: 280px; */
  /* min-height: 380px; */
  min-height: 390px;
  background: rgba(56, 29, 155, 0.729);
  position: absolute;
  top: 0;
  border-radius: 20px;
  left: 0;
  padding: 10px;
  z-index: 1202;
}
.alltheinputs {
  position: relative;
  top: 20px;
}
.pratim {
  position: relative;
  top: 40px;
  text-align: right;
  font-size: 30px;
}
.ze {
  border-bottom: 3px solid red;
}
.butons {
  position: relative;
  float: left;
}
.selecto {
  position: relative;
  left: 80px;
}
.ktzatSmall {
  position: relative;
  left: 80px;
}
.AddMahlaka {
  background: rgba(9, 183, 163, 0.836);
  width: 560px;
  height: 370px;
  position: absolute;
  top: 0;
  right: 0;
  /* top: 45px;
  right: 16.4%; */
  border-radius: 10px;
  padding: 20px;
}
.smotDEPARTMENTtitle {
  text-align: center;
  font-size: 30px;
  border-bottom: 3px solid black;
}
.smotDEPARTMENT {
  position: relative;
  top: 33px;
  float: right;
}
.depart-item {
  margin-bottom: 10px;
  border-right: 8px solid red;
  font-size: 25px;
}
.divshelemaala {
  background: rgba(0, 0, 0, 0.81);
  position: absolute;
  top: 0;
  width: 220%;
  height: 40px;
  z-index: 200;
  right: 0;
}
.hazeshemehase {
  position: absolute;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.608);
  top: 0;
  left: 0;
  z-index: 300;
}
.inputicatica {
  position: relative;
  left: 150px;
}
.r {
  position: relative;
  top: 30px;
  text-align: right;
  font-family: "Lucida Sans", "Lucida Sans Regular", "Lucida Grande",
    "Lucida Sans Unicode", Geneva, Verdana, sans-serif;
  font-size: 25px;
}
.butoon {
  position: absolute;
  top: 14%;
  left: 2%;
  width: 150px;
  height: 40px;
}
.tze {
  position: absolute;
  bottom: 0;
  left: 0;
}
.Depart {
  /* position: absolute;
  left: 520px;
  top: 2px; */
  z-index: 400;
  color: white;
  font-size: 25px;
}
.m {
  font-size: 25px;
  /* position: absolute;
  top: 2px;
  right: 38%; */
  z-index: 900;
  color: white;
}
.onlyPhone {
  font-size: 50px;
  position: absolute;
  top: 220px;
  left: 70px;
}
.logo {
  position: absolute;
  top: 0;
  right: 44px;
  color: white;
  z-index: 1000;
  font-size: 28px;
  border-bottom: 3px solid white;
}
.dropdown {
  position: absolute;
  top: 3px;
  left: 35px;
  color: white;
  z-index: 1002;
}
.dropdown ::before {
  position: relative;
  left: 7px;
}
.el-dropdown-link {
  color: white;
  width: 200px;
  font-size: 25px;
}
.el-dropdown-link:hover {
  color: rgba(255, 255, 255, 0.652);
  width: 210px;
  font-size: 25px;
}
.el-dropdown-menu {
  background: rgba(0, 0, 0, 0.79);
}
.el-icon-message-solid {
  position: absolute;
  top: 0;
  right: 250px;
  z-index: 468;
  color: rgb(194, 194, 246);
  font-size: 35px;
}
.icon {
  position: absolute;
  top: 0;
  right: 230px;
  z-index: 500;
}
.ZeYafe {
  position: fixed;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.708);
  z-index: 1200;
}
.inZeYafe {
  width: 600px;
  height: 410px;
  background: rgb(255, 255, 255);
  position: absolute;
  left: 30%;
  top: 20%;
}
.expand-enter-active {
  animation: expandAnimation 2s;
}
@keyframes expandAnimation {
  from {
    opacity: 0;
    transform: scale(0);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}
.expand-leave-active {
  animation: swirlOut 1s;
}
@keyframes swirlOut {
  from {
    opacity: 1;
    transform: scale(1);
  }
  to {
    opacity: 0;
    transform: scale(0);
  }
}

.el-icon-close {
  position: absolute;
  top: 0;
  right: 0;
  font-size: 20px;
  z-index: 1500;
}
.el-icon-close:hover {
  background: rgb(176, 157, 157);
  cursor: pointer;
}
</style>
<style>
body {
  background: rgb(241, 226, 226);
  overflow-x: hidden;
}
input::placeholder {
  text-align: right;
}
</style>
