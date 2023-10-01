<!-- eslint-disable vue/valid-v-slot -->
<template>
  <v-container>
    <v-dialog v-model="dialog" width="500">
      <template v-slot:activator="{ on, attrs }">
        <v-btn color="info " dark v-bind="attrs" v-on="on" class="mb-4">
          Add Data <v-icon dark>mdi-plus</v-icon>
        </v-btn>
      </template>

      <v-card>
        <v-card-title class="text-h5">
          {{ editmode == true ? "Edit Data" : "Add data" }}
        </v-card-title>

        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12" sm="6" md="4">
                <v-text-field
                  v-model="editedItem.firstName"
                  label="First Name"
                  required
                ></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="4">
                <v-text-field
                  v-model="editedItem.lastName"
                  label="Last Name"
                  required
                ></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="4">
                <v-text-field
                  v-model="editedItem.emailId"
                  label="Email Id"
                  required
                ></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="4">
                <v-text-field
                  v-model="editedItem.phoneNumber"
                  label="Phone Number"
                  required
                ></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="4">
                <v-text-field
                  v-model="editedItem.employeeId"
                  label="Employee Id"
                  required
                ></v-text-field>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>

        <v-divider></v-divider>

        <v-card-actions>
          <v-btn
            color="red"
            text
            @click="
              dialog = false;
              editmode = false;
              Object.assign(editedItem, defaultdata);
            "
          >
            Cancel
          </v-btn>
          <v-spacer></v-spacer>
          <v-btn
            color="success"
            text
            @click="editdata()"
            v-if="editmode == true"
          >
            Save
          </v-btn>

          <v-btn color="success" text @click="adddata()" v-else> Save </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <template>
      <v-data-table
        :headers="headers"
        :items="empdata"
        :items-per-page="5"
        class="elevation-1"
      >
        <template v-slot:item.actions="{ item }">
          <v-icon
            small
            class="mr-2"
            @click="
              editmode = true;
              editid = item.id;
              dialog = true;
              Object.assign(editedItem, item);
            "
          >
            mdi-pencil
          </v-icon>
          <v-icon small @click="deletedata(item)"> mdi-delete </v-icon>
        </template></v-data-table
      >
    </template>
  </v-container>
</template>

<script>
import axios from "axios";
export default {
  name: "HelloWorld",

  data: () => ({
    dialog: false,
    editmode: false,
    editid: "",
    headers: [
      {
        text: "id",
        align: "start",
        sortable: false,
        value: "id",
      },
      { text: "firstName", value: "firstName" },
      { text: "lastName", value: "lastName" },
      { text: "emailId", value: "emailId" },
      { text: "phoneNumber", value: "phoneNumber" },
      { text: "employeeId", value: "employeeId" },
      { text: "Actions", value: "actions", sortable: false },
    ],
    editedItem: {
      firstName: "",
      lastName: "",
      emailId: "",
      phoneNumber: "",
      employeeId: "",
    },
    defaultdata: {
      firstName: "",
      lastName: "",
      emailId: "",
      phoneNumber: "",
      employeeId: "",
    },
    empdata: [],
  }),
  mounted() {
    this.getdata();
  },
  methods: {
    //method to call the api and get the data from the database
    getdata() {
      const _this = this;
      axios
        .get("http://localhost:9090/api/v1/employees")
        .then((response) => {
          _this.empdata = response.data;
        })
        .catch((error) => {
          console.log(error);
        });
    },
    adddata() {
      const _this = this;
      axios
        .post("http://localhost:9090/api/v1/employees", _this.editedItem)
        .then((response) => {
          console.log(response);
          _this.getdata();
          _this.dialog = false;
          _this.getdata();
        })
        .catch((error) => {
          console.log(error);
        });
    },
    editdata() {
      const _this = this;
      axios
        .put(
          `http://localhost:9090/api/v1/employees/${_this.editid}`,
          _this.editedItem
        )
        .then((response) => {
          console.log(response);
          _this.getdata();
          _this.dialog = false;
          _this.editmode = false;
          Object.assign(_this.editedItem, _this.defaultdata);
        })
        .catch((error) => {
          console.log(error);
        });
    },
    deletedata(item) {
      const _this = this;
      axios
        .delete(
          `http://localhost:9090/api/v1/employees/${item.id}`,
          _this.editedItem
        )
        .then((response) => {
          console.log(response);

          _this.getdata();
        })
        .catch((error) => {
          console.log(error);
        });
    },
  },
};
</script>
