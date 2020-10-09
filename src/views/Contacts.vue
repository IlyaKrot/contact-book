<template>
  <div class="contacts-container">
    <AddContact
      @add-contact = "addContact"
    />
    <ul class="contacts">
      <li class="contacts__item" v-for="contact in contacts" :key="contact.id">
        <div class="contacts__item-container" @click="openInfo(contact.id)">
          <p class="contacts__item_name">{{ contact.name }}</p>
          <div class="contacts__item_email" v-if="contact.contacts.email">
            <img src="../assets/email.png" alt="email: ">
            <p>{{ contact.contacts.email }}</p>
          </div>
          <div class="contacts__item_phone" v-if="contact.contacts.phone">
            <img src="../assets/phone.png" alt="phone: ">
            <p> {{ contact.contacts.phone }}</p>
          </div>
          <div class="contacts__item_address" v-if="contact.contacts.address">
            <img src="../assets/address.png" alt="address: ">
            <p>{{ contact.contacts.address }}</p>
          </div>
        </div>
        <button class="contacts__item_remove" @click="openPopup(contact.id)">&times;</button>
      </li>
    </ul>
    <div ref='rm' class="popup-remove-contact">
      <PopupRemoveContact
        :contactId = 'contactId'
        :contacts = 'contacts'
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
      contactId: 0,
      contacts: [
        {
          id: 0,
          name: 'Ilya',
          contacts: {
            email: 'ilya008800@yandex.ru',
            phone: '89162273508'
          }
        },
        {
          id: 1,
          name: 'Olya',
          contacts: {
            email: 'Olya228@yandex.ru',
            phone: '88005553535'
          }
        },
        {
          id: 2,
          name: 'Anna',
          contacts: { 
            email: 'Ann1337@gmail.com',
            phone: '89153562684'
          }
        },
      ]
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
      console.log(this.$refs.rm)
      this.$refs.rm.setAttribute(
        'style',
        'position: sticky; transform: translate(0, 0); opacity: 1; z-index: 1;'
      )
    },
    closePopup() {
      this.contactId = 0;
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
