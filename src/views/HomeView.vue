<template>
  <div class="btn-container">
    <button class="add-btn" @click="addBtn()">Add</button>
    <button class="save-btn" @click="saveBtn()">Save</button>
    <button class="update-btn" @click="updateBtn()">Update</button>
  </div>
  <div class="form-container">
    <table>
      <thead>
        <tr>
          <th>Name</th>
          <th>Birthday</th>
          <th>Salary</th>
          <th>Address</th>
        </tr>
      </thead>
      <tbody>
        <tr class="input-container" v-for="employee in Data" :key="employee.Name">
          <td><input type="text" v-model="employee.Name"></td>
          <td><input type="date" :value="formatDate(employee.DateOfBirth)" @input="updateDate($event, employee)"></td>
          <td><input type="range" min="0" max="100000" step="1" value="50000" v-model="employee.Salary"></td>
          <td><input type="text" v-model="employee.Address"></td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  data() {
    return {
      Data: []
    }
  },
  methods: {
    async fetchData() {//獲取資料
      try {
        fetch('http://nexifytw.mynetgear.com:45000/api/Record/GetRecords')
        const response = await fetch("http://nexifytw.mynetgear.com:45000/api/Record/GetRecords");
        const data = await response.json();
        this.Data = data.Data;
      } catch (error) {
        console.error('Error fetching data:', error);
      }
    },
    async saveData() {//儲存資料
      try {
        const response = await fetch("http://nexifytw.mynetgear.com:45000/api/Record/SaveRecords", {
          method: "POST",
          headers: {
            "Content-Type": "application/json"
          },
          body: JSON.stringify(this.Data)
        });

        if (!response.ok) {
          throw new Error("Failed to save data");
        }
      } catch (error) {
        console.error("Error saving data:", error);
      }
    },
    formatDate(dateString) {
      // 用split把Ｔ字元提出分成一個陣列[日期,時間],因為只要前面的日期,所以index是0
      return dateString.split('T')[0];
    },
    updateDate(event, employee) {
      // 更新日期为 ISO 格式,event.target.value就是日期,再把時間加回去後回傳
      employee.DateOfBirth = `${event.target.value}T00:00:00`;
    },
    updateBtn() {
      this.fetchData();
    },
    addBtn() {
      // 用unshift把新的一組空白資料推到Data陣列的最前面
      this.Data.unshift({
        Name: "",
        DateOfBirth: "",
        Salary: 0,
        Address: ""
      });
    },
    async saveBtn() {
      try {
        await this.saveData();
        await this.fetchData();
      } catch (error) {
        console.error("Error saving data:", error);
      }
    },
  }
}
</script>

<style lang="scss">
#app {
  width: 100%;
}

.btn-container {
  width: 100%;
  display: flex;
  justify-content: space-between;

  .add-btn {
    width: 60px;
    height: 30px;
    font-size: 18px;
    border: none;
    border-radius: 4px;
    color: white;
    cursor: pointer;
    background-color: #0f6cfe;
  }

  .save-btn {
    width: 60px;
    height: 30px;
    font-size: 18px;
    border: none;
    border-radius: 4px;
    color: white;
    cursor: pointer;
    background-color: #178854;
  }

  .update-btn {
    width: 80px;
    height: 30px;
    font-size: 18px;
    border: none;
    border-radius: 4px;
    color: white;
    cursor: pointer;
    background-color: #DA3646;
  }
}

.form-container {
  table {
    width: 100%;
    margin-top: 30px;
    border-spacing: 0;

    thead {
      ::after {
        content: '';
        display: block;
        border-bottom: 2px solid rgb(234, 234, 234);
        padding-bottom: 10px;
      }

      tr {
        th {
          text-align: start;
          padding: 0 0 5px 0;
        }
      }
    }

    tbody {

      .input-container {
        td {
          padding: 5px 0 10px 0;
          border-bottom: 2px solid rgb(234, 234, 234);

          &:nth-child(3) {
            input {
              width: 70%;
            }
          }
        }
      }
    }
  }
}
</style>