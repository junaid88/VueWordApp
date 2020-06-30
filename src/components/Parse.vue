<template>
  <div id="app" data-app>
    <v-container id="dropdown-example">
      <v-row justify="center">
        <v-col cols="auto">
          <v-card width="600" height="300" raised>
            <v-card-title>German Voccabulary Test</v-card-title>
            <br />
            <v-card-text>
              <v-file-input
                accept=".txt"
                label="Click here to select a .csv voccabulary file"
                outlined
                v-model="chosenFile"
              ></v-file-input>
            </v-card-text>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn right @click="upload">Read File</v-btn>
            </v-card-actions>
          </v-card>
        </v-col>
      </v-row>
      <v-row>
        <v-col cols="12" sm="4">
          <p>Select Topic</p>

          <v-overflow-btn
            class="my-2"
            :items="distictChapter"
            item-text="selected"
            item-value="selected"
            v-model="selected"
            target="#dropdown-example"
          ></v-overflow-btn>
        </v-col>
        <v-col cols="12" sm="4">
          <p>Number of elements</p>

          <v-overflow-btn
            class="my-2"
            :items="dropdown_edit"
            editable
            item-text="wordsCount"
            item-value="wordsCount"
            v-model="wordsCount"
            target="#dropdown-example" 
          ></v-overflow-btn>
        </v-col>
</v-row><v-row>
        <v-col cols="12" sm="4">
          <v-btn rounded color="primary" v-on:click="onStartTest" dark>Start Test</v-btn>
        </v-col>
        <v-col cols="12" sm="4">
          <v-btn rounded color="primary" v-on:click="onCheck" dark>Check</v-btn>
        </v-col>
      </v-row>
     </v-container>

    <div >
      <v-simple-table   >
        <template v-slot:default>
          <thead>
            <tr>
              <th class="text-left ,h2">English Word</th>
              <th class="text-left, h1">Translation</th>
            </tr>
          </thead>
          <tbody>
            <tr
              v-for="(user,index)  in randomWords"
              :key="index"
              
            >
              <td :class="{'wrong': (user.isCorrect == false),'correct': (user.isCorrect == true)}">{{ user.English }}</td>
              <td>
                <v-text-field v-model="user.input" solo></v-text-field>
              </td>
            </tr>
          </tbody>
        </template>
      </v-simple-table>
    </div>
  </div>
</template>

<script>
import Papa from "papaparse";
export default {
  name: "parse",
  data() {
    return {
      chosenFile: null,
      selected: "All",
      rows: [
        {
          row: []
        }
      ],
      randomWords: [],
      wordsCount: 20,
      doc: null,
      dropdown_edit: [20, 30, 50, 100],
      distictChapter: ["All"]
    };
  },
  methods: {
    upload() {
      const that = this;

      const reader = new FileReader();
      reader.onload = () => {
        Papa.parse(reader.result, {
          header: true,
          skipEmptyLines: "greedy",
          complete(results) {
            console.log("complete", results);
            that.doc = results.data;
            that.distictChapter = ["All"];
            Array.prototype.push.apply(
              that.distictChapter,
              that.doc
                .map(item => item.Chapter)
                .filter((value, index, self) => self.indexOf(value) === index)
            );
            that.distictChapter.sort();
          },
          error(errors) {
            console.log("error", errors);
          }
        });
      };
      reader.readAsText(that.chosenFile);
    },
    onStartTest: function() {
      this.randomWords = [];
      for (let i = 0; i < this.wordsCount; ) {
        var rand = this.doc[Math.floor(Math.random() * this.doc.length)];
        if (this.randomWords[rand]) continue;
        //console.log(JSON.stringify(rand));
        rand.input = "";
        rand.isCorrect = null;
        this.randomWords.push(rand);
        i++;
      }
    },
    onCheck: function() {
      var correctCount=0;
      this.randomWords.forEach(function(item, index) {
        console.log(index + "" + this[index].input);
        if (
          this[index].input.toLowerCase() == this[index].German.toLowerCase()
        ) {
          item.isCorrect = true;
          correctCount++;
          console.log("sfasd");
        } else {
          item.isCorrect = false;
        }
        this.splice(index, item);
      }, this.randomWords);
      alert("Number of Correct Words :"+correctCount);
    },
    parseJSONtoCSV() {
      return Papa.unparse(this.doc);
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.wrong {
  color: red;
  
}
.correct {
  color: green;
 
}
  
</style>