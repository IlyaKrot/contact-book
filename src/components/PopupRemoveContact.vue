<template>
  <div class="remove-contact-container" ref="rem">
    <div class="remove-contact">
      <h2>Are you sure?</h2>
      <button v-if="contactId > -1" @click="$emit('confirm-delete', contactId)" class="remove-contact__yes">Yes</button>
      <button v-if="contactKey" @click="$emit('confirm-delete', contactKey)" class="remove-contact__yes">Yes</button>
      <button @click="$emit('cancel-delete')" class="remove-contact__no">No</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'PopupRemoveContact',
  props: {
    contactId: Number,
    contactKey: String
  },
}
</script>

<style lang="less" scoped>
  .remove-contact-container {
    padding: 15px;
    background: #fff;
    border-radius: 10px;
    box-shadow: 0 0 20px -10px gray;
  }
  .remove-contact__yes,
  .remove-contact__no {
    border-radius: 5px;
    margin-bottom: 15px;
    padding: 15px 50px;
    outline: none;
    border: none;
    color: #fff;
    box-shadow: 0 2px 5px -2px gray;

    &:hover {
      opacity: 0.7;
      cursor: pointer;
    }
  }

  .remove-contact__yes{
    background: #42b983;
  }
  
  .remove-contact__no{
    background: rgb(255, 96, 96);
    margin-left: 15px;
  }
</style>