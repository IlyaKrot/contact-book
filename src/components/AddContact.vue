<template>
  <div class="add-contact-container">
    <h3>ADD CONTACT</h3>
    <form class="add-contact-container__form" @submit.prevent="addContact">
      <input type="text" class="add-contact-container__form-item name" placeholder="name" v-model="name">
      <div class="add-contact-container__form-item container">
        <input type="email" class="add-contact-container__form-item-half email" placeholder="email" v-model="email">
        <input type="number" class="add-contact-container__form-item-half phone" placeholder="phone" v-model="phone">
      </div>
      <textarea class="add-contact-container__form-item other" cols="30" rows="6" v-model="other" placeholder="address: xxxxxxx; zip: xxxxx; ..."></textarea><br>
      <button ref="submit" class="add-contact-container__form-submit" type="submit">ADD</button>
    </form>
  </div>
</template>

<script>
export default {
  name: 'AddContact',
  data() {
    return {
      name: '',
      email: '',
      phone: '',
      other: ''
    }
  },
  methods: {
    addContact() {
      if (this.name.length && (this.email.length || this.phone.length)) {
        const newContact = {
          id: Date.now(),
          name: this.name,
          contacts: {}
        }
        this.email.length ? newContact.contacts['email'] = this.email : false
        this.phone.length ? newContact.contacts['phone'] = this.phone : false
        if (this.other.indexOf(':') > 0) {
          for (let i of this.other.split(';')) {
            for (let j = 1; j <= i.split(':').length; j += 2) {
              newContact.contacts[i.split(':')[j - 1].trim()] = i.split(':')[j].trim()
            }
          }
        }
        this.$emit('add-contact', newContact)
        this.name = this.email = this.phone = this.other = '';
        this.added()
      } else {
        this.error()
      }
    },
    added() {
      this.$refs.submit.textContent = 'ADDED!'
      this.$refs.submit.setAttribute(
        'style',
        'background-color: #55e7a5'
      )
      setTimeout(() => {
        this.$refs.submit.textContent = 'ADD'
        this.$refs.submit.setAttribute(
        'style',
        'background-color: #42b983'
      )
      }, 1000)
    },
    error() {
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

  .add-contact-container__form {
    display: flex;
    flex-direction: column;
  }

  .container {
    display: flex;
    justify-content: space-between;
  }

  .add-contact-container__form-item {
    width: 50%;
    margin: 0 auto;
    margin-bottom: 15px;
  }

  .add-contact-container__form-item-half {
    width: 100%;
  }

  .email {
    margin-left: -15px;
  }

  .phone {
    margin-right: -15px;
    margin-left: 15px;
  }

  input, textarea {
    outline: none;
    background:#FAFAFA;
    border: 1px solid #ccc;
    border-radius: 5px;
    padding: 10px 15px;
  }

  textarea {
    resize: none;
  }

  .add-contact-container__form-submit {
    border-radius: 5px;
    margin: 0 auto;
    margin-bottom: 15px;
    padding: 15px 0;
    outline: none;
    width: 30%;
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