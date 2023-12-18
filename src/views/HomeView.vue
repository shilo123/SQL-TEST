<template>
  <!-- v-show="shomes" -->
  <div
    v-loading="!shomes"
    element-loading-text="Loading..."
    element-loading-spinner="el-icon-loading"
    element-loading-background="rgba(0, 0, 0, 0.8)"
    ref="DIVOS"
    style="
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      z-index: 9999;
    "
  >
    <i class="el-icon-s-fold" @click="drawer = true"></i>
    <el-button
      v-if="mehika"
      type="primary"
      class="buttonOfBackOfPhone"
      @click="mehika = false"
      >חזור</el-button
    >

    <el-input
      v-model="serch"
      :placeholder="pleace"
      class="inpOnlyPhone"
      v-show="window.innerWidth <= 400"
    ></el-input>
    <!-- v-show="true" -->
    <div>
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
        <div
          @click="
            showZeYafe = true;
            showManagers = true;
            ManagA = true;
          "
        >
          <el-row class="row">
            <el-col :span="24"><i class="el-icon-s-custom"></i> מנהלים</el-col>
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
        <el-table-column label="עדכון" v-if="!mehika">
          <template slot-scope="scope">
            <el-button type="primary" size="mini" @click="updateOved(scope.row)"
              ><i class="el-icon-edit"></i> עדכן עובד</el-button
            >
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
          <div class="UpOv" v-show="showUPdateOved && showZeYafe">
            <div class="pratimOFoved-title">פרטי עובד</div>
            <div class="pratimOFoved">
              <div>
                שם עובד : <span class="HazeShelHanu">{{ UPoved.shem }}</span>
              </div>
              <div>
                שם מחלקה :
                <span class="HazeShelHanu">{{ UPoved.Department }}</span>
              </div>
              <div>
                שם תפקיד :
                <span class="HazeShelHanu">{{ UPoved.position }}</span>
              </div>
            </div>
            <div class="borderbotoom"></div>
            <div class="UPnew-title">עדכון</div>
            <div class="UPnew">
              <el-row class="UpOv-item">
                <el-col :span="24">
                  <el-input
                    v-model="UPoved.shem"
                    placeholder="שם"
                    clearable
                  ></el-input>
                </el-col>
              </el-row>
              <el-row class="UpOv-item">
                <el-col :span="24">
                  <el-input
                    clearable
                    v-model="UPoved.position"
                    placeholder="תפקיד"
                    ref="pos"
                  ></el-input>
                </el-col>
              </el-row>
              <el-row class="UpOv-item">
                <el-col :span="24">
                  <el-select
                    v-model="UPoved.Department"
                    placeholder="מחלקה"
                    class="selectC"
                  >
                    <el-option
                      v-for="(d, i) in Department"
                      :key="i"
                      :value="d"
                      class="selctC-item"
                    >
                    </el-option>
                  </el-select>
                </el-col>
              </el-row>
            </div>
            <el-button
              type="success"
              class="shmor"
              :loading="loadingButton"
              @click="UPDATEoved"
              ref="buttonS"
              >שמור</el-button
            >
            <el-button
              type="danger"
              class="sgor"
              @click="
                showUPdateOved = false;
                showZeYafe = false;
              "
              >סגור</el-button
            >
          </div>
          <div class="Managers" v-show="showZeYafe && showManagers">
            <div v-show="showZeYafe && showManagers && ManagA" class="ManagA">
              <el-table :data="netunim" class="tableManag">
                <el-table-column
                  label="שם המחלקה"
                  prop="DepartmentName"
                ></el-table-column>
                <el-table-column
                  label="מנהל מחלקה"
                  prop="Manger"
                ></el-table-column>
                <el-table-column label="החלף מנהל">
                  <template slot-scope="scope">
                    <el-button
                      type="primary"
                      @click="switchManager(scope.row)"
                      size="mini"
                      >החלף</el-button
                    >
                  </template>
                </el-table-column>
              </el-table>
            </div>
            <div class="managB" v-show="showZeYafe && showManagers && ManagB">
              <el-select
                v-model="selcoz"
                placeholder="בחר"
                class="selectD"
                @change="ManagC = true"
              >
                <el-option value="עובד קיים"></el-option>
                <el-option value="מנהל חדש"></el-option>
              </el-select>
            </div>
            <div
              class="managC"
              v-show="
                showZeYafe && showManagers && ManagC && selcoz === 'עובד קיים'
              "
            >
              <el-table :data="coputedData" height="350">
                <el-table-column label="שם עובד" prop="Name"></el-table-column>
                <el-table-column label="בחר">
                  <template slot-scope="scope">
                    <el-button
                      type="primary"
                      size="mini"
                      @click="NewManager(scope.row)"
                      >בחר</el-button
                    >
                  </template>
                </el-table-column>
              </el-table>
            </div>
            <div
              class="managC"
              v-show="
                showZeYafe && showManagers && ManagC && selcoz === 'מנהל חדש'
              "
            >
              חדש
            </div>
          </div>
        </div>
      </transition>
    </div>
    <el-drawer title="אפשרויות" :visible.sync="drawer" size="50%" class="draw">
      <div class="hazeshebatzadOFphone">
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
        <div
          @click="
            showZeYafe = true;
            showManagers = true;
            ManagA = true;
          "
        >
          <el-row class="row">
            <el-col :span="24"><i class="el-icon-s-custom"></i> מנהלים</el-col>
          </el-row>
        </div>

        <div v-for="n in 7" :key="n" @click="showZeYafe = true">
          <el-row class="row">
            <el-col :span="24">{{ `סתם ליופי -${n}` }}</el-col>
          </el-row>
        </div>
      </div>
      <!-- </div> -->
    </el-drawer>
  </div>
</template>
<script>
import { URL } from "@/URL/url";
export default {
  data() {
    return {
      window,
      data: [],
      data2: [],
      serch: "",
      selcto: "שם עובד",
      selcoz: "",
      netunim: [],
      ovedHadash: {
        shem: "",
        Departmento: "",
        position: "",
      },
      UPoved: {
        ID: "",
        shem: "",
        Department: "",
        position: "",
      },
      Department: [],
      row: {},
      filterDeparment: "הכל",
      shemhadash: "",
      mehika: false,
      loadingButton: false,
      drawer: false,
      showM: false,
      NewUser: false,
      showZeYafe: false,
      shomes: false,
      showN: false,
      showUPdateOved: false,
      showManagers: false,
      ManagA: false,
      ManagB: false,
      ManagC: false,
    };
  },
  watch: {
    ManagA(val) {
      if (val) {
        this.ManagB = false;
        this.ManagC = false;
      }
    },
    ManagB(val) {
      if (val) {
        this.ManagA = false;
        this.ManagC = false;
      }
    },
    ManagC(val) {
      if (val) {
        this.ManagB = false;
        this.ManagA = false;
      }
    },
    showManagers(val) {
      if (!val) {
        this.ManagA = false;
        this.ManagB = false;
        this.ManagC = false;
      }
    },
    loadingButton(val) {
      if (this.$refs.buttonS && val) {
        let el = this.$refs.buttonS.$el;
        el.style.position = "absolute";
      }
    },
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
    drawer(newValue) {
      if (newValue) {
        this.resetOthers("drawer");
        this.NewUser = false;
        this.showM = false;
        this.showN = false;
        this.showUpdateOved = false;
        this.showManagers = false;
      } else {
      }
    },
    NewUser(newValue) {
      if (newValue) this.resetOthers("NewUser");
    },
    showM(newValue) {
      if (newValue) this.resetOthers("showM");
    },
    showN(newValue) {
      if (newValue) this.resetOthers("showN");
    },
    showUpdateOved(newValue) {
      if (newValue) this.resetOthers("showUpdateOved");
    },
    showManagers(newValue) {
      if (newValue) this.resetOthers("showManagers");
    },
  },
  computed: {
    pleace() {
      return `סנן לפי ${this.selcto}...`;
    },
    coputedData() {
      return this.data.filter(
        (e) => this.row.DepartmentName === e.DepartmentName
      );
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
    inp.style.width = "40%";
    inp.style.position = "absolute";
    inp.style.left = "587px";
    let selc = this.$refs.selctA.$el.children[0].children[0];
    selc.style.background = "rgba(255, 238, 225, 0.663)";
    let res = await this.$ax.get(URL + "Getnetunim");
    this.netunim = res.data;
    this.$refs.DIVOS.style.zIndex = "";
    this.$refs.DIVOS.style.position = "absolute";
    this.shomes = true;
    let D = await this.$ax.get(URL + "GetD");
    this.Department = D.data;
  },

  methods: {
    resetOthers(changedVar) {
      [
        "showM",
        "showN",
        "showUpdateOved",
        "showManagers",
        "NewUser",
        "drawer",
      ].forEach((varName) => {
        if (varName !== changedVar) this[varName] = false;
      });
    },
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
      this.drawer = false;
      this.NewUser = true;
      this.showM = false;
      this.showN = false;
      this.showZeYafe = true;
      this.showupdateOved = false;
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
      this.drawer = false;

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
      this.drawer = false;

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
    updateOved(row) {
      this.UPoved.shem = row.Name;
      this.UPoved.Department = row.DepartmentName;
      this.UPoved.position = row.Position;
      this.UPoved.ID = row.EmployeeID;
      this.row = row;
      this.showZeYafe = true;
      this.showUPdateOved = true;
    },
    async UPDATEoved() {
      this.loadingButton = true;
      if (
        this.UPoved.shem &&
        this.UPoved.Department &&
        this.UPoved.position &&
        this.UPoved.ID
      ) {
        let { data } = await this.$ax.put(URL + "UPovedos", this.UPoved);
        console.log(data);
        if (data) {
          this.loadingButton = false;
          this.$message.success("העובד עודכן בהצלחה");
          this.window.location.reload();
        } else {
          this.$message.error("משהו נכשל");
          this.loadingButton = false;
        }
      } else {
        this.$message.error("לא מלאת הכל");
      }
    },
    switchManager(row) {
      console.log(row);
      this.ManagB = true;
      this.row = row;
    },
    async NewManager(row) {
      console.log(row);
      let { data } = await this.$ax.post(URL + "NewManager", {
        beforManager: this.row,
        newManager: row,
      });
      console.log(data);
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
  width: 78%;
  margin-left: 50px;
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
  left: 94px;
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
  float: center;
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
  left: 25%;
  width: 300px;
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
  z-index: 800;
  /* z-index: 1200; */
}
.inZeYafe {
  width: 600px;
  height: 410px;
  /* background: rgb(255, 255, 255); */
  position: absolute;
  left: 30%;
  top: 20%;
  border-radius: 30px;
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
.hazeshebatzadOFphone {
  display: none;
}
.el-icon-s-fold {
  display: none;
}
.inpOnlyPhone {
  position: absolute;
  top: 40px;
  right: 0;
  width: 270px;
}
.UpOv {
  width: 561px;
  height: 370px;
  background: rgba(123, 167, 89, 0.786);
  padding: 10px;
  border: 20px solid rgb(109, 109, 234);
  border-radius: 20px;
}
.UpOv-item {
  /* margin-bottom: 8px; */
  width: 200px;
  margin-left: 10px;
  float: right;
}
.selectC {
  width: 100px;
  position: absolute;
  left: -320px;
  bottom: 0;
}
.UPnew {
  position: relative;
  top: 100px;
}
.UPnew-title {
  position: absolute;
  top: 180px;
  left: 45%;
  font-size: 30px;
  border-bottom: 2px solid black;
  border-width: 100%;
}
.borderbotoom {
  border-bottom: 3px solid black;
  margin-top: 20px;
}
.pratimOFoved {
  font-size: 25px;
  text-align: right;
}
.pratimOFoved-title {
  border-bottom: 3px solid black;
  font-size: 25px;
  text-align: center;
}
.HazeShelHanu {
  border-bottom: 4px solid red;
}
.shmor {
  position: absolute;
  left: 4%;
  bottom: 20px;
  width: 45.7%;
}
.sgor {
  position: absolute;
  right: 3%;
  bottom: 20px;
  width: 45.7%;
}
.Managers {
  position: absolute;
  width: 561px;
  height: 370px;
  background: rgba(163, 112, 36, 0.786);
  padding: 10px;
  border: 20px solid rgb(109, 109, 234);
  border-radius: 20px;
}
.tableManag {
  height: 330px;
  overflow-y: auto;
}
.selectD {
  position: absolute;
  top: 40%;
  left: 16%;
  width: 400px;
  height: 60px;
}
.buttonOfBackOfPhone {
  display: none;
}
@media screen and (max-width: 400px) {
  .hazeshebatzad {
    display: none;
  }
  .hazeshebatzadOFphone {
    display: inline;
  }
  .row {
    border-bottom: 3px solid black;
  }
  .logo {
    display: none;
  }
  .dropdown {
    display: none;
  }
  .selctA {
    position: absolute;
    left: 0;
  }
  .selectB {
    display: none;
  }
  .input {
    position: absolute;
    top: 0;
    left: 0;
  }
  .el-icon-message-solid {
    left: 0;
  }
  .icon {
    left: 14px;
  }
  .el-icon-s-fold {
    display: inline;
    font-size: 40px;
    position: absolute;
    right: 0;
    z-index: 1000;
  }
  .table {
    width: 99.4%;
    position: absolute;
    left: -49.9px;
  }
  .tze {
    display: none;
  }
  .draw {
    z-index: 80000;
  }
  .inZeYafe {
    left: 5%;
    width: 340px;
  }
  .theNew {
    width: 340px;
  }
  .AddMahlaka {
    width: 320px;
    left: -2%;
  }
  .inputicatica {
    position: relative;
    right: 5px;
  }
  .buttonOfBackOfPhone {
    display: inline;
    position: absolute;
    top: 0;
    left: 22%;
    z-index: 3000;
    width: 200px;
  }
}
</style>
<style>
body {
  background: rgb(241, 226, 226);
  overflow-x: hidden;
  overflow-y: hidden;
}
input::placeholder {
  text-align: right;
}
input {
  text-align: right;
}
</style>
