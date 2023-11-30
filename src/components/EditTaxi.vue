<template>
  <v-row justify="center">
    <v-dialog v-model="dialog" persistent width="1024">
      <v-form  @submit.prevent="validateAndRegister">
        <v-card>
          <v-card-title>
            <span class="text-h5">Edit</span>
          </v-card-title>
          <v-card-text>
            <v-container>
              <v-row>
                <v-col cols="12" sm="6" md="6">
                  <v-text-field
                    v-model="editedItem.Name"
                    :rules="rules"
                    label="Name"
                  
                  ></v-text-field>
                </v-col>
                <v-col cols="12" sm="6" md="6">
                  <v-text-field
                    v-model="editedItem.Surname"
                    :rules="rules"
                    label="Surname"
                    
                  ></v-text-field>
                </v-col>
                <v-col cols="12">
                  <v-text-field
                    v-model="editedItem.Email"
                    :rules="rules"
                    label="Email*"
                   
                  ></v-text-field>
                </v-col>
                <v-col cols="12" sm="6" md="4">
                  <v-text-field
                    v-model="editedItem.VehicleType"
                    :rules="rules"
                    label="VehicleType"
                   
                  ></v-text-field>
                </v-col>
                <v-col cols="12" sm="6" md="4">
                  <v-text-field
                    v-model="editedItem.Base_Fare_Price"
                    :rules="rules"
                    label="Base Fare Price"
                   
                  ></v-text-field>
                </v-col>
                <v-col cols="12" sm="6" md="4">
                  <v-text-field
                    v-model="editedItem.Base_Fare_Distance"
                    :rules="rules"
                    label="Base Fare Distance"
                
                  ></v-text-field>
                </v-col>
              </v-row>
            </v-container>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="blue-darken-1" variant="text" @click="Close()">
              Close
            </v-btn>
            <v-btn
              type="submit"
              color="blue-darken-1"
              variant="text"
            >
              Save
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-form>
    </v-dialog>
  </v-row>
</template>

<script>
export default {
  props: {
    itemToEdit: Object,
  },
  data: () => ({
    editedItem: {},
    rules: [
      (value) => {
        if (value) {
          return true;
        }
        return "You must enter data";
      },
    ],
  
    dialog: false,
  }),
  watch: {
    itemToEdit: {
      handler(newVal) {
        this.editedItem = { ...newVal };
      },
      immediate: true,
    },
  },
  methods: {
    validateAndRegister() {
      const formIsValid = this.validateForm();
      if (formIsValid) {
        this.saveChanges();
      }
    },
    validateForm() {
      for (const rule of this.rules) {
        const resulName = rule(this.editedItem.Name);
        const resultSurname = rule(this.editedItem.Surname);
        const resultEmail = rule(this.editedItem.Email);
        const resultVehicleType = rule(this.editedItem.VehicleType);
        const resultBase_Fare_Price = rule(this.editedItem.Base_Fare_Price);
        const resultBase_Fare_Distance = rule(this.editedItem.Base_Fare_Distance);

        if (resulName !== true || 
            resultSurname !== true || 
            resultEmail !== true || 
            resultVehicleType !== true || 
            resultBase_Fare_Price !== true || 
            resultBase_Fare_Distance !== true ) {
          return false;
        }
      }
      return true;
    },
    saveChanges() {
      this.$emit("save", this.editedItem);
      this.dialog = false;
    },
    Close() {
      this.dialog = false;
      this.itemTaxi = {
        id: Math.floor(Math.random() * 100),
        Name: "",
        Surname: "",
        Email: "",
        VehicleType: "",
        Base_Fare_Price: "",
        Base_Fare_Distance: "",
        Price_Min: "",
      };
    },
  },
};
</script>

<style>
</style>