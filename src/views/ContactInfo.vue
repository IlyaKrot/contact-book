<template>
  <div class="contact-info-container" v-if="contact.length !== 0">
    <form class="contact-info-container__form" ref="form" v-if="contact[0]">
      <h3>CONTACT INFORMATION</h3>
      <div class="contact-info-container__form_item name" >
        <input type="text" :value="contact[0].name" disabled>
        <span class="remove">&times;</span>
      </div>
      <div class="contact-info-container__form_item" v-for="(item, key) in contact[0].contacts" :key="key">
        <input type="text" :value="item" disabled>
        <span class="remove">&times;</span>
      </div>
      <div class="contact-info-container__form_item" v-for="(field, key) in fields" :key="key">
        <input type="text" 
          class="field-content"
          placeholder="enter name: value" 
          @keyup='updateField(key)'
          @blur='updateField(key)'
          @paste='updateField(key)'
          @delete='updateField(key)'
        >
        <span class="remove">&times;</span>
        <span class="submit" @click="submitField(key)">OK</span>
      </div>
    </form>
    <div class='contact-info-container__add-button'
        @click='addField'>
        <img src="../assets/plus.png" alt="plus"/>
    </div>
    <button>SAVE</button>
  </div>
</template>

<script>
export default {
  name: 'ContactInfo',
  data() {
    return {
      contact: [],
      fields: []
    }
  },
  mounted() {
    if (localStorage.getItem('contacts')) {
      try {
        this.contact = JSON.parse(localStorage.getItem('contacts'));
      } catch (e) {
        localStorage.removeItem('contacts');
      }
    }
    this.getContact()
    window.scroll(0, 0);
  },
  methods: {
    getContact() {
      this.contact = this.contact.filter(c => c.id == this.$route.params.id)
      console.log(this.$route.params.id, this.contact)
    },
    addField() {
      this.fields.push({
        name: 'name',
        value: 'value'
      })
    },
    updateField(id) {
      let content = this.$el.querySelector('.field-content').value;
      let submit = this.$el.querySelector('.submit')
      if (content.indexOf(':') > 0) {
        submit.style = 'display: block'
        content = content.split(':')
        this.fields[id].name = content[0].trim();
        this.fields[id].value = content[1].trim();
      } else {
        submit.style = 'display: none'
      }
    },
    submitField(id) {
      this.contact[0].contacts[this.fields[id].name] = this.fields[id].value
      console.log(this.contact[0].contacts)
    }
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

  .contact-info-container__form_item {
    position: relative;
    display: inline;

    .submit {
      position: absolute;
      opacity: 0;
      display: none;
      margin: 0;
      padding: 0;
      top: -3px;
      bottom: -6px;
      background: #42b983;
      right: -50px;
      padding: 5px;
      color: #fff;
      border-radius: 5px;
      z-index: 1;
      transition: all 0.5s;

      &:hover {
        cursor: pointer;
      }
    }

    .remove {
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

    &:hover .remove,
    &:hover .submit{
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

    &:focus .submit,
    &:focus .remove {
      opacity: 0.8;
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
</style>