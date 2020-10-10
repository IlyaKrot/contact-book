<template>
  <div class="contact-info">
    <div class="contact-info-container" v-if="contact.length !== 0">
      <form class="contact-info-container__form" ref="form" v-if="contact[0]">
        <h3>CONTACT INFORMATION</h3>
        <div class="contact-info-container__form-item name">
          <input type="text"
            class="name-content"
            :value="contact[0].name"
            @keyup='updateName'
            @paste='updateName'
            @delete='updateName'>
          <span class="name-submit" @click="submitName">OK</span>
        </div>
        <div class="contact-info-container__form-item" v-for="(item, key, index) in contact[0].contacts" :key="key">
          <input type="text"
            :value="item"
            class="contact-content"
            @keyup='updateContact(index)'
            @paste='updateContact(index)'
            @delete='updateContact(index)'
          >
          <span class="contact-remove"  @click="openPopup(key)">&times;</span>
          <span class="contact-submit" @click="submitContact(key, index)">OK</span>
        </div>
        <div class="contact-info-container__form-item" v-for="(field, key) in fields" :key="key">
          <input type="text" 
            class="field-content"
            placeholder="enter name: value" 
            @keyup='updateField(key)'
            @paste='updateField(key)'
            @delete='updateField(key)'
          >
          <span class="field-remove" @click="removeField(key)">&times;</span>
          <span class="field-submit" @click="submitField(key)">OK</span>
        </div>
      </form>
      <div class='contact-info-container__add-button'
          @click='addField'>
          <img src="../assets/plus.png" alt="plus"/>
      </div>
      <button @click="saveContacts">SAVE</button>
    </div>
    <div class="contact-info__save-info" ref="save">
        <p ref="saveTitle">Сhanges saved successfully!</p>
        <div><router-link class="router-link" to="/">go back</router-link> or <p class="discard" @click="discardChanges">discard changes</p></div>
    </div>
    <div ref='rm' class="popup-remove-contact">
        <PopupRemoveContact
            :contactKey = 'key'
            @cancel-delete = 'cancelDelete'
            @confirm-delete = 'confirmDelete'
          />
    </div>
  </div>
</template>

<script>
import PopupRemoveContact from '@/components/PopupRemoveContact.vue'

export default {
  name: 'ContactInfo',
  components: {
    PopupRemoveContact
  },
  data() {
    return {
      contacts: [],
      contactsBefore: [],
      contact: [],
      fields: [],
      key: ''
    }
  },
  mounted() {
    this.getContact()
    window.scroll(0, 0);
  },
  methods: {
    getContact() {
      if (localStorage.getItem('contacts')) {
        try {
          this.contacts = JSON.parse(localStorage.getItem('contacts'));
        } catch (e) {
          localStorage.removeItem('contacts');
        }
      }
      this.contactsBefore = JSON.parse(JSON.stringify(this.contacts));
      this.contact = this.contacts.filter(c => c.id == this.$route.params.id)
    },
    addField() {
      this.fields.push({
        name: 'name',
        value: 'value'
      })
    },
    updateName() {
      this.$el.querySelector('.name-submit').style = 'display: block'
    },
    submitName() {
      this.contact[0].name = this.$el.querySelector('.name-content').value
      this.$el.querySelector('.name-submit').style = 'display: none'
    },
    updateContact(index) {
      let submit = this.$el.querySelectorAll('.contact-submit')
      submit[index].style = 'display: block'
    },
    submitContact(id, index) {
      let content = this.$el.querySelectorAll('.contact-content');
      this.contact[0].contacts[id] = content[index].value
      let submit = this.$el.querySelectorAll('.contact-submit')
      submit[index].style = 'display: none'
    },
    removeField(id) {
      this.fields.splice(id, 1);
    },
    updateField(id) {
      let content = this.$el.querySelectorAll('.field-content')
      content = content[id].value
      let submit = this.$el.querySelectorAll('.field-submit')
      if (content.indexOf(':') > 0) {
        submit[id].style = 'display: block'
        content = content.split(':')
        this.fields[id].name = content[0].trim();
        this.fields[id].value = content[1].trim();
      } else {
        submit[id].style = 'display: none'
      }
    },
    submitField(id) {
      this.contact[0].contacts[this.fields[id].name] = this.fields[id].value
      let submit = this.$el.querySelectorAll('.field-submit')
      submit[id].style = 'display: none'
      this.$forceUpdate();
      this.fields.pop();
    },
    saveContacts() {
      let name = this.$el.querySelector('.name-content')
      this.contact[0].name = name.value
      const parsed = JSON.stringify(this.contacts);
      localStorage.setItem('contacts', parsed);
      this.$refs.save.setAttribute(
        'style',
        'transform: translateY(0px); opacity: 1'
      )
      this.$refs.saveTitle.setAttribute(
        'style',
        'opacity: 1'
      )
      setTimeout(() => {
        this.$refs.saveTitle.setAttribute(
          'style',
          'opacity: 0'
        )
      }, 2000)
    },
    discardChanges() {
      const parsed = JSON.stringify(this.contactsBefore);
      localStorage.setItem('contacts', parsed);
      this.getContact();
    },
    openPopup(key) {
      this.key = key
      this.$refs.rm.setAttribute(
        'style',
        'position: sticky; transform: translate(0, 0); opacity: 1; z-index: 3;'
      )
    },
    closePopup() {
      this.key = '';
      this.$refs.rm.setAttribute(
        'style',
        'position: sticky; transform: translate(0, 50px); opacity: 0;'
      )
      setTimeout(() => {
        this.$refs.rm.setAttribute(
          'style',
          'position: absolute; z-index: -1;'
        )
      }, 500)
    },
    cancelDelete() {
      this.closePopup()
    },
    confirmDelete(key) {
      delete this.contact[0].contacts[key]
      this.$forceUpdate();
      this.closePopup()
    },
  }
}
</script>

<style lang="less" scoped>
  .contact-info-container {
    position: relative;
    padding: 15px;
    color: #2c3e50;
    display: flex;
    flex-direction: column;
    margin-bottom: 15px;
    z-index: 2;
    background: #fff;
    border-radius: 10px;
    box-shadow: 0 0 20px -10px gray;
  }

  .contact-info-container__add-button {
    display: inline-block;
    height: 2rem;
    width: 2rem;
    line-height: 2.25rem;
    text-align: center;
    background:  #42b983;
    border-radius: .3rem;
    border-radius: 50%;
    cursor: pointer;
    opacity: 0.5;
    transition: all 210ms cubic-bezier(.96,.03,.94,.64);
    margin: 15px auto;
    font-size: 1.4rem;
    outline: none;

    &:hover,
    &:focus {
      transform: scale(1.2);
      opacity: 0.8;
    }

    &:active {
      transform: scale(.8);
    }
    img {
      height: 60%;
      width: 60%;
      margin: 0 auto;
      filter: invert();
    }
  }

  .contact-info-container__form-item {
    position: relative;
    display: inline;

    .contact-submit,
    .field-submit,
    .name-submit {
      display: none;
      position: absolute;
      opacity: 0;
      margin: 0;
      padding: 0;
      top: -3px;
      bottom: -6px;
      background: #42b983;
      right: -50px;
      padding: 5px;
      color: #fff;
      border-radius: 5px;
      // z-index: 1;
      transition: all 0.5s;

      &:hover {
        cursor: pointer;
      }
    }

    .contact-remove,
    .field-remove {
      position: absolute;
      opacity: 0;
      top: 1px;
      margin: 0;
      padding: 0;
      background: rgb(255, 96, 96);
      right: 15px;
      width: 18px;
      height: 18px;
      color: #fff;
      border-radius: 5px;
      z-index: 1;
      transition: all 0.5s;

      &:hover {
        cursor: pointer;
      }
    }

    &:hover .contact-remove,
    &:hover .field-remove,
    &:hover .contact-submit,
    &:hover .field-submit,
    &:hover .name-submit{
      opacity: 0.8;
    }
  }

  input {
    border-radius: 5px;
    width: 50%;
    color: #848484;
    margin: 0 auto;
    margin-bottom: 15px;
    background:#FAFAFA;
    padding: 10px 15px;
    outline: none;
    border: none;

    &:hover {
      cursor: pointer;
    }

    &:focus {
      color: #2c3e50;
      cursor: text;
    }
  }

  button {
    border-radius: 5px;
    margin: 15px auto;
    padding: 15px 0;
    width: 30%;
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

  .popup-remove-contact {
    position: absolute;
    bottom: 0;
    z-index: -1;
    margin: 0 auto;
    margin-bottom: 15px;
    transform: translate(0, 50px);
    width: 50%;
    height: 30%;
    opacity: 0;
    transition: all 0.5s;
  }

  .contact-info__save-info {
    padding: 15px;
    opacity: 0;
    transform: translateY(-100px);
    transition: all 0.5s;

    :first-child {
      color: #42b983;
      opacity: 0;
      transition: all 0.5s;
    }

    :last-child {
      display: inline;
    }

    .router-link,
    .discard {
      color: #815A8E;
      text-decoration: underline;
      cursor: pointer;
      opacity: 1;
    }
  }
</style>