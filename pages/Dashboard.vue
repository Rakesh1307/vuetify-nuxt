<template>
  <div class="pa-2">
    <h3>Details</h3>
    <v-data-table :headers="headers" :items="usersList" :items-per-page="5" class="elevation-1">
      <template v-slot:[`item.actions`]="{ item }">
        <v-icon small @click="deleteItem(item)">mdi-delete</v-icon>
      </template>
    </v-data-table>
    <v-form ref="userForm" v-model="valid">
      <v-row>
        <v-col cols="12" sm="6" md="12">
          <v-text-field
            v-model="newUser.id"
            :rules="[rules.required, rules.minValue, rules.checkIdExists]"
            type="number"
            label="Enter Id"
          ></v-text-field>
        </v-col>
        <v-col cols="12" sm="6" md="12">
          <v-text-field :rules="[rules.required]" v-model="newUser.name" label="Enter Name"></v-text-field>
        </v-col>
        <v-col cols="12" sm="6" md="12">
          <v-text-field
            v-model="newUser.age"
            :rules="[rules.required, rules.minValue]"
            type="number"
            label="Enter Age"
          ></v-text-field>
        </v-col>
      </v-row>
      <v-btn color="primary" :disabled="!valid" @click="addItem()">
        <v-icon left>mdi-plus</v-icon>Add
      </v-btn>
    </v-form>
  </div>
</template>

<script lang="ts">
import Vue from "vue";
import { Component } from "vue-property-decorator";

interface User {
  id: number;
  name: string;
  age: number;
}

@Component
export default class DashboardComponent extends Vue {
  valid = true;
  disabled = true;
  newUser = {} as User;

  headers = [
    { text: "Id", value: "id" },
    { text: "Name", value: "name" },
    { text: "Age", value: "age" },
    { text: "Actions", value: "actions", sortable: false },
  ];

  usersList: User[] = [];

  rules = {
    required: (value: number | string) => !!value || "Required.",
    minValue: (value: number) => value > 0 || "Value should be greater than 0",
    checkIdExists: (value: number) =>
      !this.usersList.some((user) => user.id === value) || "Id already exists",
  };

  deleteItem(item: User): void {
    const index = this.usersList.indexOf(item);
    this.usersList.splice(index, 1);
  }

  addItem(): void {
    this.usersList.push(this.newUser);
    this.usersList.sort((elementA, elementB) => elementA.id - elementB.id);
    this.newUser = {} as User;
    (this.$refs.userForm as Vue & {
      resetValidation: () => void;
    }).resetValidation();
  }
}
</script>
