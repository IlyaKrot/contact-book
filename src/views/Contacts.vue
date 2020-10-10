<template>
  <div class="contacts-container">
    <AddContact
      @add-contact = "addContact"
    />
    <ul class="contacts">
      <li class="contacts__item" v-for="contact in contacts" :key="contact.id">
        <div class="contacts__item-container" @click="openInfo(contact.id)">
          <p class="contacts__item_name">{{ contact.name }}</p>
          <div class="contacts__item_contacts" v-for="(value, key) in contact.contacts" :key="key">
            <img v-if="key === 'email' || key === 'phone' || key === 'address'" :src="imgs[key]" :alt="key">
            <p v-if="key === 'email' || key === 'phone' || key === 'address'">{{ value }}</p>
          </div> 
        </div>
        <button class="contacts__item_remove" @click="openPopup(contact.id)">&times;</button>
      </li>
    </ul>
    <div ref='rm' class="popup-remove-contact">
      <PopupRemoveContact
        :contactId = 'contactId'
        @cancel-delete = 'cancelDelete'
        @confirm-delete = 'confirmDelete'
      />
    </div>
  </div>
</template>

<script>
import AddContact from '@/components/AddContact.vue';
import PopupRemoveContact from '@/components/PopupRemoveContact.vue'

export default {
  name: 'Contacts',
  components: {
    AddContact,
    PopupRemoveContact
  },
  data() {
    return {
      contactId: -1,
      imgs: {
        'email': '/img/email.png',
        'phone': '/img/phone.png',
        'address': '/img/address.png'
      },
      contacts: []
    }
  },
  mounted() {
    if (localStorage.getItem('contacts')) {
      try {
        this.contacts = JSON.parse(localStorage.getItem('contacts'));
      } catch (e) {
        localStorage.removeItem('contacts');
      }
    }
  },
  methods: {
    addContact(obj) {
      this.contacts.unshift(obj)
      this.saveContacts()
    },
    openPopup(id) {
      this.contactId = id
      this.$refs.rm.setAttribute(
        'style',
        'position: sticky; transform: translate(0, 0); opacity: 1; z-index: 1;'
      )
    },
    closePopup() {
      this.contactId = -1;
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
    confirmDelete(id) {
      this.contacts = this.contacts.filter(c => c.id !== id)
      this.closePopup()
      this.saveContacts()
    },
    saveContacts() {
      const parsed = JSON.stringify(this.contacts);
      localStorage.setItem('contacts', parsed);
    },
    openInfo(id) {
      this.$router.push({path: `/info/${id}`})
    }
  }
}
</script>

<style lang="less" scoped>
  .contacts__item {
    position: relative;
    color: #2c3e50;
    margin-bottom: 15px;
    background: #fff;
    border-radius: 10px;
    box-shadow: 0 0 20px -10px gray;
    overflow: hidden;
    text-overflow: ellipsis;

    &:hover {
      background: #EDEEF0;
      cursor: pointer;
    }

    &:active {
      box-shadow: 0 0 15px -10px gray;
    }

    div {
      display: flex;
      justify-content: center;
      align-items: center;
      p {
        display: inline-block;
      }
      img {
        float: left;
        opacity: 0.8;
        margin-right: 10px;
        height: 18px;
        width: 18px;
      }
    }
  }
  .contacts__item-container {
    display: flex;
    flex-direction: column;
    padding: 15px;
  }
  .contacts__item_name {
    font-size: 20px;
    font-weight: bold;
  } 

  .contacts__item_remove {
    position: absolute;
    border: none;
    outline: none;
    background: rgb(255, 96, 96);
    top: 15px;
    right: 15px;
    width: 20px;
    height: 20px;
    color: #fff;
    border-radius: 5px;
    z-index: 1;

    &:hover {
      cursor: pointer;
      opacity: 0.5;
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
</style>
