<template>
  <div class="hello">
    <h1>Team Generator</h1>
    <form>
      <!-- <div class="form-group">
        <label for="file">Members CSV file:</label>
        <input class="form-control" id="file" type="file" ref="members" @change="upload">
      </div>-->
      <div class="form-group">
        <label for="file">Teams:</label>
        <input class="form-control" type="number" min="2" v-model="teamNumber">
      </div>
      <div class="form-group">
        <label for="members">Members:</label>
        <textarea class="form-control" v-model="content"></textarea>
        <br>
        <div class="form-group">
          <input type="button" class="btn btn-primary" value="Generate" @click="generate">
        </div>
      </div>
    </form>
    <br>
    <div>Result:</div>
    <div class="result">
      <div class="row">
        <div class="col-md-6 team" v-for="(team, index) in teams" v-bind:key="index">
          <div class="title">Team {{index + 1}}</div>
          <div class="member">
            <span
              v-for="(member, index2) in team.member"
              v-bind:key="index2"
            >{{ member.name }} ({{member.year}})&nbsp;</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  data() {
    return {
      teamNumber: 2,
      content: "K ,1987\nA ,1978",
      members: [],
      teams: []
    };
  },
  methods: {
    getTeam: function() {
      var index = 0;
      for (var i = 0; i < this.teams.length; i++) {
        // Get size of team
        var objectMemberLength = this.teams[index].member.length;
        var objectSum = this.teams[index].totalAge;
        var currentMemberLength = this.teams[i].member.length;
        var currentSum = this.teams[i].totalAge;

        // Choose team
        if (currentMemberLength == 0) {
          index = i;
          break;
        } else if (currentMemberLength < objectMemberLength) {
          index = i;
        } else if (
          currentMemberLength == objectMemberLength &&
          currentSum > objectSum
        ) {
          index = i;
        }
      }
      return index;
    },
    generate: function() {
      this.teams = [];
      this.members = [];

      for (var i = 1; i <= this.teamNumber; i++) {
        var team = {
          totalAge: 0,
          member: []
        };
        this.teams.push(team);
      }

      var lines = this.content.split("\n");
      for (var line = 0; line < lines.length; line++) {
        var lineContent = lines[line].split(",");
        var member = {
          name: lineContent[0].trim(),
          year: parseInt(lineContent[1].trim())
        };
        // Push member to array
        this.members.push(member);
      }
      // Sort member by birthYear
      this.members = _.sortBy(this.members, [
        function(o) {
          return o.year;
        }
      ]);
      // Add members to teams
      for (var i = 0; i < this.members.length; i++) {
        var index = this.getTeam();
        this.teams[index].totalAge += this.members[i].year;
        this.teams[index].member.push(this.members[i]);
      }
      console.log(this.teams);
    }
    // upload: function(ev) {
    //   var file = ev.target.files[0];
    //   var reader = new FileReader();
    //   reader.onload = function(progressEvent) {
    //     // By lines
    //     var members = [];
    //     var lines = this.result.split("\n");
    //     for (var line = 0; line < lines.length; line++) {
    //       var lineContent = lines[line].split(",");
    //       var member = {
    //         name: lineContent[0].trim(),
    //         year: parseInt(lineContent[1].trim())
    //       };
    //       // Push member to array
    //       members.push(member);
    //     }

    //     // Sort member by birthYear
    //     members = _.sortBy(members, [
    //       function(o) {
    //         return o.year;
    //       }
    //     ]);

    //     // Inital Teams
    //     var teams = [
    //       {
    //         totalAge: 0,
    //         member: []
    //       },
    //       {
    //         totalAge: 0,
    //         member: []
    //       },
    //       {
    //         totalAge: 0,
    //         member: []
    //       }
    //     ];
    //     var index = this.getTeam(teams);

    //     // Add members to teams
    //     members.forEach(function(item, index) {
    //       teams[index].totalAge += item.year;
    //       teams[index].member.push(item);
    //     });
    //   };
    //   reader.readAsText(file, this);
    // }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.result {
  background-color: #e6faff;
  padding: 15px;
}

.member {
  background-color: #ccf5ff;
}
</style>
