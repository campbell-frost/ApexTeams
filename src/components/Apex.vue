<template>
  <v-container>
    <v-responsive class="align-center text-center fill-height px-4">
      <v-row class="d-flex justify-center mb-3">
        <p class="title">Apex Teams</p>
      </v-row>
      <v-row>
        <v-col>
          <v-text-field
            v-model="inputName"
            @keydown.enter="addName"
            placeholder="Input Name"
            class="input-field"
          ></v-text-field>
        </v-col>
      </v-row>
      <v-row>
        <v-col>
          <v-btn @click="removeSelected" block>Remove Selected</v-btn>
        </v-col>
        <v-col>
          <v-btn @click="undo" block>Undo</v-btn>
        </v-col>
        <v-col>
          <v-btn @click="generate" block>Generate</v-btn>
        </v-col>
      </v-row>
      <v-row>
        <v-col>
          <v-card class="name-card">
            <v-list dense>
              <v-list-item
                v-for="(name, index) in originalNames"
                :key="index"
                :class="{ 'selected': isSelected(index) }"
                @click="toggleSelection(index)"
              >
                <v-list-item-title>
                  {{ name }}
                  <v-spacer></v-spacer>
                </v-list-item-title>
              </v-list-item>
            </v-list>
          </v-card>
        </v-col>
      </v-row>
      <v-row v-if="groupsData.length > 0">
        <v-col>
          <v-card v-for="(group, index) in groupsData" :key="index">
            <v-card-title>Group {{ index + 1 }}</v-card-title>
            <v-list>
              <v-list-item v-for="item in group" :key="item">
                <v-list-item-title>{{ item }}</v-list-item-title>
              </v-list-item>
            </v-list>
          </v-card>
        </v-col>
      </v-row>
    </v-responsive>
  </v-container>
</template>

<script>
export default {
  data() {
    return {
      inputName: "",
      originalNames: [],
      names: [],
      numberOfGroups: null,
      groupsData: [],
      selectedItems: new Set() // Set to store selected item indexes
    };
  },
  methods: {
    addName() {
      if (this.inputName.trim() !== "") {
        this.originalNames.push(this.inputName);
        this.names.push(this.inputName);
        this.inputName = ""; // Clear the input after adding the name
      }
    },
    removeSelected() {
      const selectedIndexes = Array.from(this.selectedItems);
      selectedIndexes.sort((a, b) => b - a); // Sort in descending order for proper removal
      selectedIndexes.forEach(index => {
        this.originalNames.splice(index, 1);
        this.names.splice(index, 1);
      });
      this.selectedItems.clear(); // Clear selected items
      this.updateGroups();
    },
    undo() {
      this.originalNames = [];
      this.names = [];
      this.selectedItems.clear();
      this.groupsData = [];
    },
    generate() {
      this.shuffleNames();
      this.updateGroups();
    },
    shuffleNames() {
      // Fisher-Yates shuffle algorithm
      for (let i = this.names.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [this.names[i], this.names[j]] = [this.names[j], this.names[i]];
      }
    },
    updateGroups() {
      const numItems = this.names.length;
      const numGroups = Math.floor(numItems / 3); // Calculate the number of groups
      this.groupsData = [];
      for (let i = 0; i < numGroups; i++) {
        const start = i * 3;
        const end = start + 3;
        this.groupsData.push(this.names.slice(start, end));
      }
    },
    toggleSelection(index) {
      if (this.isSelected(index)) {
        this.selectedItems.delete(index);
      } else {
        this.selectedItems.add(index);
      }
    },
    isSelected(index) {
      return this.selectedItems.has(index);
    }
  },
};
</script>

<style>
.title {
  padding-top: 50px;
  font-size: 50px;
}
.input-field {
  width: 100%;
}
.name-card {
  height: 350px; /* Adjust the height as per your requirement */
  overflow-y: auto; /* Add scrollbar if content exceeds the height */
}
.selected {
  background-color: lightblue; /* Add your preferred highlight color */
}
.underlined {
  text-decoration: underline;
}
.v-messages {
  min-height: 0px;
}
/* Hide v-input__details class */
.v-input__details {
  display: none;
}
</style>
