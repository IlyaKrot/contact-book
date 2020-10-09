<template>
  <div class="add-contact-container">
    <h3>ADD CONTACT</h3>
    <form class="add-contact-container__form" @submit.prevent="addContact">
      <input type="text" class="add-contact-container__form_name" placeholder="name" v-model="name"><br>
      <textarea class="add-contact-container__form_contacts" cols="30" rows="10" v-model="contacts" placeholder="email: xxx@xxxx.xxx; phone: x xxx-xxx-xx-xx; ..."></textarea><br>
      <button ref="submit" class="add-contact-container__form_submit" type="submit">ADD</button>
    </form>
  </div>
</template>

<script>
export default {
  name: 'AddContact',
  data() {
    return {
      name: '',
      contacts: '',
    }
  },
  methods: {
    addContact() {
      if (this.name.length && this.contacts.length) {
        let parseContact = {};
        for (let i of this.contacts.split(';')) {
          for (let j = 1; j <= i.split(':').length; j += 2) {
            parseContact[i.split(':')[j - 1].trim()] = i.split(':')[j].trim()
          }
        }
        const newContact = {
          id: Date.now(),
          name: this.name,
          contacts: parseContact
        }
        console.log(newContact)
        this.$emit('add-contact', newContact)
        this.name = this.contacts = '';
        this.added()
      } else {
        this.error()
      }
    },
    added() {
      console.log(this.$refs)
      this.$refs.submit.textContent = 'ADDED!'
      setTimeout(() => {
        this.$refs.submit.textContent = 'ADD'
      }, 3000)
    },
    error() {
      console.log(this.$refs)
      this.$refs.submit.textContent = 'ERROR!'
      this.$refs.submit.setAttribute(
        'style',
        'background-color: rgb(255, 96, 96)'
      )
      setTimeout(() => {
        this.$refs.submit.textContent = 'ADD'
        this.$refs.submit.setAttribute(
          'style',
          'background-color: #42b983'
        )
      }, 1000)
    }
  }
}
</script>

<style lang="less" scoped>
  .add-contact-container {
    position: relative;
    padding: 15px;
    color: #2c3e50;
    margin-bottom: 15px;
    background: #fff;
    border-radius: 10px;
    box-shadow: 0 0 20px -10px gray;
  }

  h2 {
    text-decoration: underline;
  }

  input, textarea {
    border-radius: 5px;
    width: 50%;
    margin: 0 auto;
    margin-bottom: 15px;
    padding: 10px 15px;
    outline: none;
    background:#FAFAFA;
    border: 1px solid #ccc;
  }

  textarea {
    resize: none;
  }

  button {
    border-radius: 5px;
    margin-bottom: 15px;
    padding: 15px 50px;
    outline: none;
    border: none;
    background: #42b983;
    color: #fff; 
    font-weight: bold;
    box-shadow: 0 2px 5px -2px gray;
    transition: all 0.5s;

    &:hover {
      opacity: 0.7;
      cursor: pointer;
    }

    &:active {
      box-shadow: 0 0 0 0 gray;
    }
  }
</style>