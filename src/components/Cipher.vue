<template lang="pug">
  div.container
    .text-center
      h1.mt-4 Cipher Encoder/Decoder
      h5 An online tool to encode/decode Caesar, Shift and Affine ciphers
    .my-5
      h5 Method
      div
        b-form-radio-group(buttons, button-variant="outline-primary", v-model="method", :options="options")
    .my-5
      h5 Message
      div
        b-form-textarea(cols="50", rows="5", autofocus, v-model="message")
    .my-5(v-if="method !== 'caesar'")
      h5
        span Settings
      div(v-if="method === 'affine'")
        span a:
        input.form-control.encryption-key.mx-2(type="number", v-model="affineAParameter")
        span b:
        input.form-control.encryption-key.mx-2(type="number", v-model="affineBParameter")
      div(v-if="method === 'shift'")
        span Rotate By:
        input.form-control.encryption-key.mx-2(type="number", v-model="affineBParameter")
    .my-5
      h5
        span Encrypted/Decrypted Message
      div
        div.encrypted-message.p-2 {{ affineCipher || '&nbsp;' }}
      <!--template(v-for="a in [1, 3, 5, 7, 9, 11, 15, 17, 19, 21, 23, 25]")-->
        <!--div(v-for="b in _.range(0, 26)")-->
          <!--div.encrypted-message.p-2.my-1 {{ "a: " + a + ", b: " + b}} => {{ affineCipherEncryption(a, b) }}-->

    .my-5
      h5 Letters Dictionary
      div
        b-table(:small="true", :bordered="true", :striped="true", :fixed="true", :hover="true", :items="[charToCode]")

    .my-5
      h5 Message Frequency Table
      div
        table.table.table-striped.table-bordered.table-hover
          thead
            tr
              th Character
              th Frequency
          tbody
            tr(v-for="chr in Object.entries(frequency).sort((b, a) => {return a[1] - b[1]})")
              td {{ chr[0].toUpperCase() }}
              td {{ chr[1] }}
    .dropdown-divider
    div.footer.pb-5.text-center.small
      a(href="https://github.com/i0/cipher-solver") Github
      span &nbsp;|&nbsp;
      span Made by ❤ @ May 2018 by &nbsp;
      a(href="https://github.com/i0/", target="_blank") i0
      span &nbsp;

</template>

<script>
export default {
  created() {
    document.title = "Cipher Encoder/Decoder";
  },
  data() {
    return {
      method: "caesar",
      options: [
        { text: "Caesar Cipher", value: "caesar" },
        { text: "Shift Cipher", value: "shift" },
        { text: "Affine Cipher", value: "affine" }
      ],
      charToCode: {
        a: 0,
        b: 1,
        c: 2,
        d: 3,
        e: 4,
        f: 5,
        g: 6,
        h: 7,
        i: 8,
        j: 9,
        k: 10,
        l: 11,
        m: 12,
        n: 13,
        o: 14,
        p: 15,
        q: 16,
        r: 17,
        s: 18,
        t: 19,
        u: 20,
        v: 21,
        w: 22,
        x: 23,
        y: 24,
        z: 25
        // ',': 26,
        // '.': 27,
        // '?': 28,
        // ':': 29,
        // '\'': 30,
        // '"': 31,
        // ' ': 32,
      },
      message: "meet me after the toga party",
      frequency: {},
      encryptionKeyInput: "-3",
      affineAParameter: "1",
      affineBParameter: "3"
    };
  },
  watch: {
    message(val) {
      this.calculateFrequency(val.toLowerCase());
    }
  },
  mounted() {
    this.calculateFrequency(this.message.toLowerCase());
  },
  methods: {
    calculateFrequency(msg) {
      this.frequency = {};
      msg.split("").forEach(chr => {
        if (!this.frequency.hasOwnProperty(chr)) {
          this.frequency[chr] = 1;
        } else {
          this.frequency[chr]++;
        }
      });
    },
    affineCipherEncryption: function(a, b) {
      let msg = this.message.toLowerCase();
      let encrypted = "";
      let keySpaceSize = this._.size(this.charToCode);
      for (let i = 0; i < msg.length; i++) {
        let chr = msg[i];
        let encryptedChar = `${chr}`;
        if (this.charToCode.hasOwnProperty(chr)) {
          let x = this.charToCode[chr];
          let offset = a * x + b;
          let encryptedCode = offset % keySpaceSize;
          if (encryptedCode < 0) {
            encryptedCode = keySpaceSize + encryptedCode;
          }
          encryptedChar = this.codeToChar[encryptedCode];
        }
        encrypted += encryptedChar;
      }
      return encrypted;
    }
  },
  computed: {
    affineCipher: function() {
      let A = this.method === "affine" ? parseInt(this.affineAParameter) : 1;
      let B = this.method === "caesar" ? 3 : parseInt(this.affineBParameter);
      return this.affineCipherEncryption(A, B);
    },
    codeToChar: function() {
      return this._.invert(this.charToCode);
    },
    encryptionKey: function() {
      if (!this.encryptionKeyInput) {
        return 0;
      }
      return parseInt(this.encryptionKeyInput);
    },
    encryptedMessage2: function() {
      let msg = this.message.toLowerCase();
      let encrypted = "";
      let keySpaceSize = this._.size(this.charToCode);
      for (let i = 0; i < msg.length; i++) {
        let chr = msg[i];
        let encryptedChar = `${chr}`;
        if (this.charToCode.hasOwnProperty(chr)) {
          let charCode = this.charToCode[chr];
          let encryptedCode = (charCode + this.encryptionKey) % keySpaceSize;
          if (encryptedCode < 0) {
            encryptedCode = keySpaceSize + encryptedCode;
          }
          encryptedChar = this.codeToChar[encryptedCode];
        }
        encrypted += encryptedChar;
      }
      return encrypted;
    }
  }
};
</script>

<style>
input.encryption-key {
  width: 100px;
  display: inline;
}

.encrypted-message {
  background: #eee;
  color: black;
  font-style: italic;
  font-size: 20px;
  display: inline-block;
  letter-spacing: 3px;
  word-break: break-all;
}
</style>
