<template>
  <transition name="modal">
    <div class="modal">
      <div class="modal__wrapper">
        <div class="modal__container">
          <slot name="header">
          </slot>
          <div class="modal__body">
            <slot name="body">
            </slot>
          </div>
          <div class="modal__footer">
            <slot name="footer">
              <button class="modal-default-button" @click="$emit('close')">
                Закрыть
              </button>
            </slot>
          </div>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
  export default {
    name: 'modal',
    data () {
      return {
        name: '',
        focus: false,
        error: ''
      }
    },
    computed: {
      required() {
        return this.errorMessage ? this.errorMessage : this.error;
      },
      missingName() {
        return this.name === '';
      }
    },
    methods: {
      onFocus() {
        this.focus = true;
      }
    }
  }
</script>

<style lang="scss" scoped>
  .modal {
    position: fixed;
    z-index: 9998;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, .5);
    display: flex;
    transition: opacity .3s ease;

    &__wrapper{
      flex: 1 1 auto;
      align-self:center;
    }

    &__container {
      width: 400px;
      margin: 0px auto;
      padding: 30px 40px;
      background-color: #fff;
      border-radius: 2px;
      box-shadow: 0 2px 8px rgba(0, 0, 0, .33);
      transition: all .3s ease;
      font-family: Helvetica, Arial, sans-serif;
    }
    
    &__body {
      margin: 20px 0;
    }

    &__footer {
      display: flex;
      justify-content: flex-end;
      align-items: flex-end;
      margin-top: 10px;
    }
  }

  .modal-default-button {
    background-color: cornflowerblue;
    padding: 10px;
    border-radius: 3px;
    color:white;

    &:hover:not(:disabled) {
      cursor: pointer;
      background-color: rgb(50, 117, 243);
    }
  }

  .modal-enter {
    opacity: 0;
  }

  .modal-leave-active {
    opacity: 0;
  }

  .modal-enter .modal__container,
  .modal-leave-active .modal__container {
    -webkit-transform: scale(1.1);
    transform: scale(1.1);
  }
</style>