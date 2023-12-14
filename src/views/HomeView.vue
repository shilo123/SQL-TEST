<template>
  <div v-if="shomes">
    <div v-show="window.innerWidth < 400" class="onlyPhone">
      אין תמיכה במובייל פתח מחשב!
    </div>
    <div v-show="window.innerWidth > 400">
      <div class="Depart" v-if="Department.length > 0">
        סך הכל מחלקות
        <span
          ><strong> {{ Department.length }}</strong></span
        >
      </div>
      <div class="m" v-if="data2.length > 0">
        סך הכל עובדים {{ data2.length }}
      </div>
      <div class="divshelemaala"></div>
      <div class="hazeshebatzad">
        <!-- <el-row v-for="(n, i) in 13" :key="i" class="row">
        <el-col :span="24">{{ `אפשרות-${n}` }}</el-col>
      </el-row> -->
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
            <el-col :span="24">הוסף מחלקה</el-col>
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

        <div v-for="n in 7" :key="n">
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
        <el-select v-model="selcto" placeholder="חפש לפי" class="selctA">
          <el-option
            v-for="(m, i) in ['שם עובד', 'תפקיד', 'מחלקה']"
            :key="i"
            :value="m"
            :label="m"
          ></el-option>
        </el-select>
      </div>
      <el-table :data="data" class="table" stripe>
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

        <el-table-column label="שם עובד" prop="Name"></el-table-column>
        <el-table-column label="תפקיד" prop="Position"></el-table-column>
        <el-table-column label="מחלקה" prop="DepartmentName"></el-table-column>
      </el-table>
      <el-table :data="netunim" class="tableNetunim">
        <el-table-column label="סך הכל עובדים" prop="TOTAL"></el-table-column>
        <el-table-column
          label="שם המחלקה"
          prop="DepartmentName"
        ></el-table-column>
      </el-table>
      <div v-show="NewUser" class="theNew">
        <div class="alltheinputs">
          <div style="text-align: right; font-size: 20px; margin-bottom: 10px">
            שם העובד:
          </div>
          <el-input
            style="width: 200px"
            size="small"
            v-model="ovedHadash.shem"
            placeholder="שם העובד החדש"
            class="ktzatSmall"
          ></el-input>
          <div style="text-align: right; font-size: 20px; margin-bottom: 10px">
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
          <div style="text-align: right; font-size: 20px; margin-bottom: 10px">
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
          <el-button type="success" @click="AddOved" :loading="loadingButton"
            >שמור</el-button
          >
          <el-button type="danger" @click="NewUser = false">צא</el-button>
        </div>
      </div>
      <div class="AddMahlaka" v-show="showM">
        <div style="text-align: right">שם המחלקה החדש:</div>
        <br />
        <el-input
          v-model="shemhadash"
          placeholder="שם המחלקה החדשה"
          style="width: 60%"
          class="inputicatica"
        ></el-input>
        <el-button type="success" class="butoon" @click="hosefOta"
          >הוסף</el-button
        >
        <el-button
          type="danger"
          @click="
            showM = false;
            shemhadash = '';
          "
          class="tze"
          >חזור</el-button
        >
        <div class="ze r">{{ shemhadash }}</div>
      </div>
      <div class="hazeshemehase" v-if="mehika"></div>
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
      mehika: false,
      loadingButton: false,
      shemhadash: "",
      showM: false,
      shomes: true,
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
    inp.style.width = "334px";
    let res = await this.$ax.get(URL + "Getnetunim");
    // console.log(res.data);
    this.netunim = res.data;
    let D = await this.$ax.get(URL + "GetD");
    // console.log(D.data);
    this.Department = D.data;
  },

  methods: {
    hosefOved() {
      this.NewUser = true;
      this.showM = false;
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
    },
    async hosefOta() {
      // console.log(this.Department.includes(this.shemhadash));
      if (this.shemhadash && !this.Department.includes(this.shemhadash)) {
        const { data } = await this.$ax.post(URL + "AddDepartment", {
          newP: this.shemhadash,
        });

        if (data) {
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
  background: rgba(6, 178, 135, 0.788);
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
}
.hazeshebatzad {
  position: absolute;
  top: 40px;
  left: 0;
  background: rgb(57, 158, 198);
  width: 200px;
  height: 130%;
}
.table {
  width: 30%;
  position: absolute;
  right: 24.2%;
  top: 40px;
  border: 0.4px solid black;
  padding: 3px;
  height: 80%;
  overflow: auto;
  z-index: 305;
}
.table::-webkit-scrollbar {
  display: none;
}
.input {
  width: 250px;
  background: rgba(0, 0, 0, 0.385);
  position: absolute;
  right: 371px;
  top: 0;
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
  top: 0;
  right: 704px;
  width: 120px;
  z-index: 201;
}
.tableNetunim {
  width: 24%;
  position: absolute;
  right: 0;
  top: 40px;
}
.theNew {
  width: 280px;
  min-height: 380px;
  background: rgba(56, 29, 155, 0.729);
  position: absolute;
  top: 43px;
  border-radius: 20px;
  transition: left;
  left: 25.4%;
  padding: 10px;
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
  top: 50px;
  float: right;
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
  width: 454px;
  height: 120px;
  position: absolute;
  top: 45px;
  left: 13.4%;
  border-radius: 30px;
  padding: 20px;
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
  left: 200px;
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
  top: 0;
  left: 0;
  width: 160px;
}
.tze {
  position: absolute;
  top: 0;
  left: 120px;
}
.Depart {
  position: absolute;
  right: 100px;
  top: 2px;
  z-index: 400;
  color: white;
  font-size: 25px;
}
.m {
  font-size: 25px;
  position: absolute;
  top: 2px;
  left: 31%;
  z-index: 900;
  color: white;
}
.onlyPhone {
  font-size: 50px;
  position: absolute;
  top: 220px;
  left: 70px;
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
