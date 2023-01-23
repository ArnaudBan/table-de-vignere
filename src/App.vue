<script>
export default {
  data() {
    return {
      alphabet: [..."abcdefghijklmnopqrstuvwxyz"],
      action: "code",
      mdp: "hibou",
      message: "",
      messageCode: "",
    };
  },
  watch: {
    message() {
      this.encode();
    },
  },
  methods: {
    getCharAtCoord(x, y) {
      return this.alphabet[(y + x) % 26];
    },
    getAlphabetLetterIndex(letter) {
      return parseInt(
        Object.keys(this.alphabet).find((key) => this.alphabet[key] === letter)
      );
    },
    encode() {
      let messageCode = [];
      const mdp = this.mdp;
      const getAlphabetLetterIndex = this.getAlphabetLetterIndex;
      const getCharAtCoord = this.getCharAtCoord;
      const mdpLength = this.mdpLength;
      [...this.slugMessage].forEach(function (letter, index) {
        const letterX = getAlphabetLetterIndex(letter);
        const letterY = getAlphabetLetterIndex(mdp[index % mdpLength]);
        messageCode.push(getCharAtCoord(letterX, letterY));
      });

      this.messageCode = messageCode.join("");
    },
  },
  computed: {
    mdpLength: {
      get() {
        return this.mdp.length;
      },
    },
    slugMessage: {
      get() {
        return this.message
          .toString()
          .normalize("NFD")
          .replace(/[\u0300-\u036f]/g, "")
          .toLowerCase()
          .trim()
          .replace(/\s+/g, "")
          .replace(/[^\w-]+/g, "")
          .replace(/--+/g, "");
      },
    },
  },
};
</script>

<template>
  <main>
    <div class="small-wrapper">
      <h1>Code et décode tes message secret grasse à la table de vignére</h1>
      <h2>Ton message</h2>

      <form>
        <!--      <div class="input-field">-->
        <!--        <p class="label">Tu veux faire quoi ?</p>-->
        <!--        <p>-->
        <!--          <input type="radio" v-model="action" value="code" id="code" />-->
        <!--          <label for="code">Coder un message</label>-->
        <!--        </p>-->
        <!--        <p>-->
        <!--          <input type="radio" v-model="action" value="decode" id="decode" />-->
        <!--          <label for="decode">Decoder un message</label>-->
        <!--        </p>-->
        <!--      </div>-->

        <div class="input-field">
          <label class="label" for="mdp">Mot de passe</label>
          <input type="text" v-model="mdp" id="mdp" />
        </div>
        <div class="input-field" v-if="action === 'code'">
          <label class="label" for="message">Message</label>
          <textarea v-model="message" id="message" />
        </div>

        <div class="input-field" v-if="action === 'decode'">
          <label class="label" for="messageCode">Message codé</label>
          <textarea v-model="messageCode" id="messageCode" />
        </div>
      </form>
    </div>
    <h2>Résultat</h2>
    <table class="table-result">
      <tr>
        <td v-for="(letter, index) in slugMessage">
          {{ mdp[index % mdpLength] }}
        </td>
      </tr>
      <tr>
        <th v-for="letter in slugMessage">{{ letter }}</th>
      </tr>
      <tr>
        <td v-for="(letter, index) in slugMessage">
          {{ messageCode[index] }}
        </td>
      </tr>
    </table>

    <code v-if="messageCode">
      {{ messageCode }}
    </code>

    <h2>La table de vignère</h2>
    <table class="table-vignere">
      <thead>
        <tr>
          <td></td>
          <th v-for="letter in alphabet">{{ letter }}</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(letter, index) in alphabet">
          <th>{{ letter }}</th>
          <td v-for="n in 26">
            {{ this.getCharAtCoord(index, n - 1) }}
          </td>
        </tr>
      </tbody>
    </table>
  </main>
</template>

<style scoped>
h1,
h2 {
  text-align: center;
  line-height: 1.2;
  margin-bottom: 2rem;
}
.small-wrapper {
  max-width: 750px;
  margin: 0 auto;
}
.input-field {
  margin-bottom: 1rem;
}
.label {
  display: block;
  font-weight: bold;
}
input[type="text"],
textarea {
  width: 100%;
}
.table-vignere {
  width: 100%;
}
.table-result,
.table-vignere {
  border-collapse: collapse;
}
th {
  background-color: var(--vt-c-divider-dark-2);
}
td {
  text-align: center;
}
th,
td {
  border: 1px solid var(--vt-c-black);
}
code {
  font-family: monospace;
  padding: 2rem;
  background-color: var(--vt-c-divider-dark-2);
  display: block;
  margin: 2rem 0;
}
</style>
