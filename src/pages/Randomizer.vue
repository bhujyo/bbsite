<template>
  <Layout>
    <header class="header">
      <div class="header__text-box">
        <h1 class="heading-primary">
          <span class="heading-primary--main">Dr. B's class randomizer</span>
        </h1>
      </div>
    </header>

    <main>
      <section class="section-about">
        <div class="row">
          <div class="col-1-of-2">
            <h2 class="heading-secondary">
              Students
            </h2>
            <ol>
              <li
                class="paragraph"
                v-for="(assignment, index) in randomAssignments"
                :key="index"
              >
                {{ assignment.student }} - question {{ assignment.question }}
              </li>
            </ol>
          </div>
        </div>
      </section>
    </main>
  </Layout>
</template>

<script>
export default {
  metaInfo: {
    title: "Class Randomizer",
  },
  data() {
    return {
      students: ["AF", "AH", "DK", "LW", "RC"],
      questionNumbers: [1, 2, 3, 4, 6, 8],
    };
  },
  computed: {
    randomAssignments() {
      const shuffledStudents = this.shuffle(this.students);
      const shuffledQuestions = this.shuffle(this.questionNumbers);
      const newArr = [];
      shuffledStudents.forEach((element) => {
        newArr.push({
          student: element,
          question: shuffledQuestions[shuffledStudents.indexOf(element)],
        });
      });
      return newArr;
    },
  },
  methods: {
    shuffle(myArray) {
      // This fn uses the Fisher-Yates shuffle, as described in this tutorial: https://bost.ocks.org/mike/shuffle/
      var m = myArray.length,
        t,
        i;

      // While there remain elements to shuffle…
      while (m) {
        // Pick a remaining element…
        i = Math.floor(Math.random() * m--);

        // And swap it with the current element.
        t = myArray[m];
        myArray[m] = myArray[i];
        myArray[i] = t;
      }

      return myArray;
    },
  },
};
</script>
