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
      <section class="p-8">
        <div class="justify-around md:flex">
          <div class="pr-8 md:w-1/2">
            <h2 class="text-5xl text-green-800">
              The form...
            </h2>

            <!-- Form -->
            <form @submit.prevent class="border-2 border-green-400 rounded-md">
              <!-- Student names -->
              <fieldset class="m-2">
                <legend class="text-3xl">
                  Step 1: Enter a list of students (order does not matter)
                </legend>
                <div
                  class="flex items-center"
                  v-for="(input, keya) in studentinputs"
                  :key="keya"
                >
                  <label
                    :for="`name-${keya}`"
                    class="block pr-2 text-xl text-gray-700"
                    >Student {{ keya + 1 }}:</label
                  >
                  <input
                    :id="`name-${keya}`"
                    :name="`name-${keya}`"
                    class="block px-4 py-2 mt-1 text-lg border rounded-md shadow-sm border-grey-200 min-w-18"
                    type="text"
                    v-model="input.name"
                    v-on:keydown.enter="add('studentinputs', 'name')"
                  />
                  <span class="flex">
                    <button
                      type="button"
                      class="m-2 text-xl underline"
                      @click="remove(keya, 'studentinputs')"
                    >
                      (remove student)
                    </button>
                  </span>
                </div>
                <div class="m-2">
                  <button
                    type="button"
                    class="px-4 py-2 mt-2 text-xl border-2 border-green-200 rounded-md"
                    @click="add('studentinputs', 'name')"
                  >
                    add another student
                  </button>
                </div>
              </fieldset>

              <!-- Questions -->
              <fieldset class="m-2 mt-12">
                <legend class="text-3xl">
                  Step 2: Enter a list of questions (order does not matter)
                </legend>
                <div
                  class="flex items-center"
                  v-for="(examinput, keyb) in examinputs"
                  :key="keyb"
                >
                  <label
                    :for="`exam-question-${keyb}`"
                    class="block pr-2 text-xl text-gray-700"
                    >Question {{ keyb + 1 }}:</label
                  >
                  <input
                    :id="`exam-question-${keyb}`"
                    :name="`exam-question-${keyb}`"
                    class="block px-4 py-2 mt-1 text-lg border rounded-md shadow-sm border-grey-200 min-w-18"
                    type="text"
                    v-model="examinput.question"
                    v-on:keydown.enter="add('examinputs', 'question')"
                  />
                  <span class="flex">
                    <button
                      type="button"
                      class="m-2 text-xl underline"
                      @click="remove(keyb, 'examinputs')"
                    >
                      (remove question)
                    </button>
                  </span>
                </div>
                <div class="m-2">
                  <button
                    type="button"
                    class="px-4 py-2 mt-2 text-xl border-2 border-green-200 rounded-md"
                    @click="add('examinputs', 'question')"
                  >
                    add another question
                  </button>
                </div>
              </fieldset>

              <div class="px-4 py-3 mt-12 text-right border-t-2 sm:px-6">
                <button
                  type="submit"
                  @click="submitForm"
                  class="inline-flex justify-center px-4 py-2 text-xl font-medium text-white bg-green-500 border border-transparent rounded-md shadow-sm min-w-12 hover:bg-green-700"
                >
                  Randomize assignments!
                </button>
              </div>
            </form>
          </div>

          <div class="pl-8 md:w-1/2">
            <h2 class="text-5xl text-green-800">
              ...the results!
            </h2>
            <div v-if="showResults">
              <h3 class="text-3xl">Your data</h3>
              <!-- Results -->
              <h3 class="text-3xl">Randomized students</h3>
              <ol>
                <li
                  class="text-xl list-decimal list-inside"
                  v-for="(assignment, index) in matchedResults"
                  :key="index"
                >
                  {{ assignment.student }} - {{ assignment.question }}
                </li>
              </ol>
            </div>
            <div v-else>
              Fill out the form and click "randomize assignments" to get the
              results.
            </div>
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
      studentinputs: [{ name: "" }],
      examinputs: [{ question: "" }],
      showResults: false,
      matchedResults: [],
    };
  },
  methods: {
    add(dataprop, keyname) {
      // Adapted from https://smarttutorials.net/dynamically-add-or-remove-input-textbox-using-vuejs/
      this[dataprop].push({ [keyname]: "" });
    },
    remove(index, dataprop) {
      this[dataprop].splice(index, 1);
    },
    submitForm() {
      // Clear out any empty inputs
      const cleanStudents = this.clearUnusedInputs(this.studentinputs, "name");
      const cleanQuestions = this.clearUnusedInputs(
        this.examinputs,
        "question"
      );

      // Shuffle the clean arrays
      let shuffledStudents = this.shuffle(cleanStudents);
      let shuffledQuestions = this.shuffle(cleanQuestions);

      // Create a new array of objects where random students are matched with random questions
      const matchedStudentsQuestions = [];

      shuffledStudents.forEach((oneStudent) => {
        matchedStudentsQuestions.push({
          student: oneStudent.name,
          question:
            shuffledQuestions[shuffledStudents.indexOf(oneStudent)] !==
            undefined
              ? shuffledQuestions[shuffledStudents.indexOf(oneStudent)].question
              : "Not enough questions - no question assigned",
        });
      });

      // Set data to randomized values so we can print
      this.matchedResults = matchedStudentsQuestions;

      // Don't show the results until the form has been submitted
      this.showResults = true;
    },
    clearUnusedInputs(arr, key) {
      return arr.filter((data) => data[key] !== "");
    },
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
