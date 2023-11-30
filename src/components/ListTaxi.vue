<template>
  <div style="display: flex; justify-content: space-between;">
    <v-file-input
          show-size
          counter
          multiple
          label="File input"
          @change="handleFileChange"
          class="mr-2"
        >
    </v-file-input>
    <v-btn @click="CalculateTicketPrice()" :disabled="!csvLoaded" height="55px" color="blue">Calculate ticket price</v-btn>
  </div>
  <AddTaxi @save="ClickSave" />
  <v-table class="table mt-5">
    <thead>
      <tr>
        <th>id</th>
        <th>Name</th>
        <th>Surname</th>
        <th>Email</th>
        <th>VehicleType</th>
        <th>Base_Fare_Price</th>
        <th>Base_Fare_Distance</th>
        <th>fare</th>
        <th class="text-center">Action</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="item in items" :key="item.id">
        <th>{{ item.id }}</th>
        <th>{{ item.Name }}</th>
        <th>{{ item.Surname }}</th>
        <th>{{ item.Email }}</th>
        <th>{{ item.VehicleType }}</th>
        <th>{{ item.Base_Fare_Price }}</th>
        <th>{{ item.Base_Fare_Distance }}</th>
        <th>{{ item.Price_Min }}</th>
        <th  class="text-center">
          <v-btn @click="EditTaxi(item)" class="mr-2" color="yellow" >Edit</v-btn>
          <v-btn @click="DeleteTaxi(item)" color="red">Delete</v-btn>
        </th>
      </tr>
    </tbody>
  </v-table>
  <EditTaxi
    ref="editTaxiDialog"
    :itemToEdit="editingItem"
    @save="saveEditedItem"
  ></EditTaxi>
</template>
<script>
import AddTaxi from "./AddTaxi.vue";
import EditTaxi from "./EditTaxi.vue";
import Papa from "papaparse";

export default {
  components: { AddTaxi, EditTaxi },
  data: () => ({
    items: [
      {
        id: 1,
        Name: "Dickerson",
        Surname: "Macdonald",
        Email: "Macdonald@gmail.com",
        VehicleType: "Honda",
        Base_Fare_Price: "200",
        Base_Fare_Distance: "150",
        Price_Min: "",
      },
      {
        id: 2,
        Name: "Dickerson",
        Surname: "Macdonald",
        Email: "Macdonald@gmail.com",
        VehicleType: "Honda",
        Base_Fare_Price: "300",
        Base_Fare_Distance: "250",
        Price_Min: "",
      },
      {
        id: 3,
        Name: "Dickerson",
        Surname: "Macdonald",
        Email: "Macdonald@gmail.com",
        VehicleType: "Honda",
        Base_Fare_Price: "34",
        Base_Fare_Distance: "353",
        Price_Min: "",
      },
    ],
    dialog: false,
    csvData: [],
    editingItem: null,
    Distance_Traveled: "",
    Traveled_Unit: "",
    Cost_Per_Distance_Traveled: "",
    csvLoaded: false,
    isFileInputSelected: false,
  }),
  methods: {
    ClickSave(item) {
      let max = 0;
      let newId = 0;
      for (let i = 0; i < this.items.length; i++) {
        if (this.items[i].id > max) {
          max = this.items[i].id;
        }
      }
      newId = max + 1;
      item.id = newId;
      if (this.isFileInputSelected) {
        const distance_traveled =
          (Number(this.Distance_Traveled) - Number(item.Base_Fare_Distance)) /
          Number(this.Traveled_Unit);
        if (Number(this.Distance_Traveled) > Number(item.Base_Fare_Distance)) {
          item.Price_Min =
            Number(item.Base_Fare_Price) +
            distance_traveled * Number(this.Cost_Per_Distance_Traveled);
        } else {
          item.Price_Min = Number(item.Base_Fare_Price);
        }
      }
      this.items.push(item);
    },
    DeleteTaxi(item) {
      for (let i = 0; i < this.items.length; i++) {
        if (item.id === this.items[i].id) {
          this.items.splice(i, 1);
        }
      }
    },
    handleFileChange(event) {
      const file = event.target.files[0];

      Papa.parse(file, {
        header: false,
        dynamicTyping: true,
        complete: (result) => {
          this.csvData = result.data[0];
          this.Distance_Traveled = this.csvData[0];
          this.Traveled_Unit = this.csvData[1];
          this.Cost_Per_Distance_Traveled = this.csvData[2];
          this.csvLoaded = true;
        },
      });
    },
    CalculateTicketPrice() {
      this.items.forEach((item, index) => {
        const distance_traveled =
          (Number(this.Distance_Traveled) - Number(item.Base_Fare_Distance)) /
          Number(this.Traveled_Unit);
        if (Number(this.Distance_Traveled) > Number(item.Base_Fare_Distance)) {
          item.Price_Min =
            Number(item.Base_Fare_Price) +
            distance_traveled * Number(this.Cost_Per_Distance_Traveled);
        } else {
          item.Price_Min = Number(item.Base_Fare_Price);
        }
      });
      this.isFileInputSelected = true;
    },
    EditTaxi(item) {
      this.editingItem = item;
      this.openEditDialog();
    },
    openEditDialog() {
      this.$refs.editTaxiDialog.editedItem = { ...this.editingItem };
      this.$refs.editTaxiDialog.dialog = true;
    },
    closeEditDialog() {
      this.$refs.editTaxiDialog.dialog = false;
    },
    saveEditedItem(editedItem) {
      const index = this.items.findIndex((item) => item.id === editedItem.id);
      if (index !== -1) {
        const old_Base_Fare_Price = this.items[index].Base_Fare_Price;
        const old_Base_Fare_Distance = this.items[index].Base_Fare_Distance;

        this.items.splice(index, 1, editedItem);
        if(old_Base_Fare_Price !== editedItem.Base_Fare_Price 
        || old_Base_Fare_Distance !==editedItem.Base_Fare_Distance){
          this.closeEditDialog();
          if (this.isFileInputSelected) {
            this.CalculateTicketPrice();
          }
        }
      }
      this.closeEditDialog();
    },
  },
};
</script>