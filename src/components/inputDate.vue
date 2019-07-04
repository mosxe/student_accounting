<template>
  <div class="input">
    <input :value="value"
      @input="$emit('input', $event.target.value)" 
      @focus="onFocus"
      @blur="onBlur "
      :valid="valid"
      type="text"
      @keypress="dateFormated"
      maxlength="10"
      :error="$emit('error', error)"
      class="input__item " 
      :placeholder="dateCurrent"/>
    <label :class="['input__text' , { active: value !== ''}]">{{label}}</label>
    <span v-if="error" class="input__error">{{ error}}</span>
  </div> 
</template>
  
<script>
  export default {
    name: 'input-date',
    props: {
      value: {
        type: String
      },
      label: {
        type: String,
        default: ''
      },
      errorMessage: {
        type: String,
        default: ''
      },
      valid: {
        type: Boolean
      }
    },
    data () {
      return {
        name: '',
        focus: false,
        error: ''
      }
    },
    computed: {
      dateCurrent() {
        let date = new Date();
        return date.toLocaleDateString('ru-RU');
      }
    },
    methods: {
      correctDate(val, len) {
        switch (len) {
          case 2:
            this.error = +val < 1 || +val > 31 ? 'Неверный формат даты' : '';
            break;
          case 5:
            let month = val.substring(3);
            this.error = +month < 1 || +month > 12 ? 'Неверный формат даты' : '';
            break;
          case 10:
            let years = +val.substring(6);
            let currentYears = new Date().getFullYear();
            this.error = years > currentYears ? 'Неверный формат даты' : '';
            break;
        }
      },
      dateFormated(event) {
        let length = event.target.value.length;
        if (event.keyCode < 48 || event.keyCode > 57) {
            event.preventDefault();
        }
  
        if (length !== 1 || length !== 3) {
          if (event.keyCode == 46) {
            event.preventDefault();
          }
        }
        if (length === 2) {
          event.target.value += '.';
        }
  
        if (length === 5) {
          event.target.value += '.';
        }
      },
      onFocus(e) {
        this.focus = true;
      },
      onBlur(e) {
        this.focus = false;
        if (this.value === '' && this.valid) return this.error = 'Поле обязательно к заполнению';
      }
    },
    watch: {
      value(newVal, old) {
        if(newVal.length === 2 || newVal.length === 5 || newVal.length === 10) {
          this.correctDate(newVal, newVal.length);
        }
        if (!newVal && this.focus && this.valid) {
          this.error = 'Поле обязательно к заполнению';
        } 
      }
    }
  }
</script>
  
<style lang="scss" scoped>
  .input {
    position: relative;
    margin: 30px 0;
    max-width: 400px;
    display: flex;
    flex-direction: column;
  
    &__item {
      line-height: 20px;
      border: 0;
      border-radius: 0;
      border-bottom: 2px solid #C8CCD4;
      font-size: 16px;
      background: none;
      width: 100%;
      padding: 10px 10px 3px 3px;
        
      &::-webkit-input-placeholder {
        opacity: 0;
        transition: opacity 0.2s ease-out;
      }

      &::-moz-placeholder {
        opacity: 0;
        transition: opacity 0.2s ease-out;
      }
        
      &:-ms-input-placeholder {
        color: white;
        transition: opacity 0.2s ease-out;
      }

      &:focus {
        border-bottom: 2px solid #3e85cc;
        outline: none;
        background: none;

        ~ .input__text {
          top: -12px;
          font-size: 14px;
          color:#3e85cc;
        }

        &::-webkit-input-placeholder {
          opacity: 1;
          transition: opacity 0.2s ease-out;
        }
        &::-moz-placeholder {
          opacity: 1;
          transition: opacity 0.2s ease-out;
        }
      }
    }

    &__text {
      position: absolute;
      left:0;
      top: 10px;
      transition: 0.2s ease all;
      -moz-transition: 0.2s ease all;
      -webkit-transition: 0.2s ease all;
      font-size: 18px;
      color: rgba(0,0,0,.54);
      pointer-events: none;
    }

    &__error {
      position: absolute;
      left: 0;
      font-size: 12px;
      color: red;
      word-break: break-word;
      overflow-wrap: break-word;
      word-wrap: break-word;
      padding-top: 5px;
      line-height: 1;
      bottom: -15px;
    }

    .active {
      top: -12px;
      font-size: 14px;
      color:#3e85cc;
    }
  }
</style>