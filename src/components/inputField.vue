<template>
  <div class="input">
    <input :value="value"
      @input="$emit('input', $event.target.value)"
      @focus="onFocus"
      @blur="onBlur"
      @keypress="$emit('keypress', $event)"
      :valid="valid"
      :maxlength="maxlength"
      class="input__item" :type="type" placeholder=" "/>
    <label class="input__text">{{label}}</label>
    <span v-if="required" class="input__error">{{ required}}</span>
  </div> 
</template>
  
<script>
  export default {
    name: 'input-field',
    props: {
      value: {
        type: [Number, String]
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
      },
      type: {
        type: String,
        default: 'text'
      },
      maxlength: {
        type: [Number, String],
        default: '' 
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
      required () {
        return this.errorMessage ? this.errorMessage : this.error;
      }
    },
    methods: {
      onFocus() {
        this.focus = true;
      },
      onBlur() {
        this.focus = false;
        if (this.value === '' && this.valid) return this.error = 'Поле обязательно к заполнению';
      }
    },
    watch: {
      value(newVal, old) {
        if (!newVal && this.focus && this.valid) {
           this.error = 'Поле обязательно к заполнению';
        } else if (newVal !== '') {
          this.error = '';
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
      font-size:16px;
      background: none;
      width: 100%;
      padding:10px 10px 3px 3px;

      &:focus {
        border-bottom: 2px solid #3e85cc;
        outline: none;
        background: none;

        ~ .input__text {
          top:-12px;
          font-size:14px;
          color:#3e85cc;
        }
      }

      &:not(:placeholder-shown) ~ .input__text {
        top:-12px;
        font-size:14px;
        color:#3e85cc;
      }
      
      &:not(:-moz-placeholder) ~ .input__text {
        top:-12px;
        font-size:14px;
        color:#3e85cc;
      }
      
      &:not(:-ms-input-placeholder ) ~ .input__text {
        top:-12px;
        font-size:14px;
        color:#3e85cc;
      }
    }

    &__text {
      position: absolute;
      left:0;
      top: 10px;
      transition:0.2s ease all;
      -moz-transition:0.2s ease all;
      -webkit-transition:0.2s ease all;
      font-size: 18px;
      color: rgba(0,0,0,.54);
      pointer-events: none;
    }

    &__error {
      position: absolute;
      left:0;
      font-size: 12px;
      color: red;
      word-break: break-word;
      overflow-wrap: break-word;
      word-wrap: break-word;
      padding-top: 5px;
      line-height: 1;
      bottom: -15px;
    }
  }
</style>