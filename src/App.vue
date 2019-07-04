<template>
  <div id="app">
    <h1 class="header">Учет студентов</h1>
    <button class="button button__success" @click="showModal = true">
      Добавить студента
    </button>
    <div class="filter">
      <input-field v-model.number="ageFrom" label="Возраст от" />
      <input-field v-model.number="ageTo" label="Возраст до" />
    </div>
    <div class="table">
      <div class="table__search">
        <input class="table__search__input" v-model="search" placeholder="Поиск по фамилии"/>
      </div>
      <table class="table__content">
        <thead>
          <tr>
            <th>
            </th>
            <th>
              <span>Имя</span>
            </th>
            <th @click="sort('surname')" :style="{'cursor' : 'pointer'}">
              <span>Фамилия</span>
            </th>
            <th @click="sort('age')" :style="{'cursor' : 'pointer'}">
              <span>Возраст</span>
            </th>
            <th @click="sort('group')" :style="{'cursor' : 'pointer'}">
              <span>Группа</span>
            </th>
            <th>
              <span>Редактирование</span>
            </th>
          </tr>
        </thead>
        <tbody>            
          <tr v-for="(student, index) in filteredStudents" :key="index" @click="activeRow(student,index)" :class="{active: student.checked}">
            <td>
              <input type="checkbox" class="checkbox" :checked="student.checked" :value="index">
            </td>
            <td>
              <span>{{student.name}}</span>
            </td>
            <td>
              <span>{{student.surname}}</span>
            </td>
            <td>
              <span>{{student.age}}</span>
            </td>
            <td>
              <span>{{student.group}}</span>
            </td>
            <td>
              <button  class="button button__change-table" @click.stop="editStudent(student, index)">Изменить</button>
            </td>
          </tr>
          <tr v-if="!filteredStudents.length" class="no-data">
            <td colspan="6">
              Нет данных
            </td>
          </tr>
        </tbody>
      </table>
    </div>
    <button class="button button__delete" @click="deleteStudent" :disabled="!disabledDelete">
      Удалить
    </button>
    <modal v-show="showModal" @close="showModal = false">
      <div slot="body">
        <input-field v-model="name" label="Имя" :error-message="fullNameValid(name)" ref="name" valid/>
        <input-field v-model="surname" label="Фамилия" :error-message="fullNameValid(surname)" ref="surname" valid/>
        <input-field v-model="group" label="Группа"  ref="group" valid :maxlength="10" />
        <input-date v-model="birthday" label="Дата" valid  @error="error = $event" />
      </div>
      <div slot="footer">
        <button  class="button button__success" @click="saveStudent" :disabled="!disabledSaveBtn">{{ buttonName }}</button>
        <button  class="button button__close" @click="resetModal">Закрыть</button>
      </div>
    </modal>
  </div>
</template>
  
<script>
  import inputField from '@/components/inputField';
  import inputDate from '@/components/inputDate';
  import Modal from '@/components/modal';

  export default {
    name: 'App',
    components: { inputField, inputDate, Modal },
    data() {
      return {
          name: '',
          surname: '',
          group: '',
          birthday: '',
          error: '',
          valid: true,
          showModal: false,
          ageFrom: '',
          ageTo: '',
          students: [
            {
              name:'Кирилл',
              surname: 'Алексеев',
              group: 'ПО-1-110',
              birthday: '21.09.1992',
              age: 26,
              checked: false
            }
          ],
          search: '',
          editIndex: null,
          currentSort: '',
          ascending: false
      }
    },
    computed: {
      disabledSaveBtn() {
        return this.name && this.$refs.name.errorMessage === '' && this.surname && this.$refs.surname.errorMessage === '' && this.group && 
        this.$refs.group.errorMessage === '' && this.birthday.length === 10 && this.error === '';
      },
      disabledDelete() {
        return this.students.find(i => i.checked);
      },
      disabledFilter() {
        return this.ageFrom && this.ageTo;
      },
      buttonName() {
        return this.editIndex !== null ? 'Изменить' : 'Добавить';
      },
      filteredStudents() {
        return this.students.filter(student => {
          return student.surname.toLowerCase().includes(this.search.toLowerCase())
        }).filter(student => {
          if (!this.ageFrom && !this.ageTo) {
            return student;
          } else {
            return this.ageFrom && this.ageTo ? student.age >= this.ageFrom && 
              student.age <= this.ageTo : this.ageFrom ? student.age >= this.ageFrom : student.age <= this.ageTo;
            }
        }).sort((a,b) => {
            if (a[this.currentSort] > b[this.currentSort]) {
              return this.ascending ? 1 : -1;
            } else if (a[this.currentSort] < b[this.currentSort]) {
              return this.ascending ? -1 : 1;
            }
            return 0;
        });
      }
    },
    methods: {
      editStudent(student, index) {
        this.name = student.name;
        this.surname = student.surname;
        this.group = student.group;
        this.birthday = student.birthday;
        this.editIndex = index;
        this.showModal = true;
      },
      deleteStudent() {      
        this.students = this.students.filter(item => !item.checked);
      },
      saveStudent(student, index) {
        if (this.editIndex !== null) {
          let student = this.students[this.editIndex];
          student.name = this.name;
          student.surname = this.surname;
          student.group = this.group.toUpperCase();
          student.birthday = this.birthday;
          student.age = this.age(this.birthday);      
          this.resetModal();
        } else {
          let student = {
            name: this.name,
            surname: this.surname,
            group: this.group.toUpperCase(),
            birthday: this.birthday,
            age: this.age(this.birthday),
            checked: false
          }
          this.students.push(student);
          this.resetModal();
        }     
      },
      sort(column) {
        if (this.currentSort === column) {
          this.ascending = !this.ascending;
        } else {
          this.currentSort = column;
          this.ascending = true;
        }
      },
      checkStudent(student) {
        student.checked = !student.checked;
      },
      activeRow(student, index) {
        student.checked = !student.checked;
      },
      fullNameValid(val) {
        const rule = /^[а-яА-ЯёЁa-zA]+$/;
        if (val !== '' && !rule.test(val)) {
          return 'Поле должно содержать только буквы'
        } 
      },
      resetModal() {
        this.showModal = false;
        this.editIndex = null;
        this.name = '';
        this.surname = '';
        this.group = '';
        this.birthday = ''; 
      },
      age(date) {     
        let currentDate = new Date();
        let birthDate = new Date(date.replace( /(\d{2}).(\d{2}).(\d{4})/, "$2/$1/$3"));
        let age = currentDate.getFullYear() - birthDate.getFullYear();
        let month = currentDate.getMonth() - birthDate.getMonth();

        if (month < 0 || (month === 0 && currentDate.getDate() < birthDate.getDate())) {
          age = age - 1;
        }
        return age;
      }
    },
    mounted() {
      window.addEventListener('keyup', event => {
        if (event.keyCode === 46 && !this.showModal) {
          this.deleteStudent();
        }
      });
    }
  }
</script>
  
<style lang="scss" scoped>
  .table {
    width: 100%;
    overflow-x: auto;
    overflow-y: hidden;
    margin: 10px 0;
    display: flex;
    flex-direction: column;

    &__search {
      margin: 20px 0;
      align-self: flex-end;
            
      &__input {
        border-bottom: 2px solid rgba(138, 137, 139, 0.568);
        background: none;
        outline: none;
        font-size: 16px;

        &:focus {
          border-bottom: 2px solid rgba(57, 98, 211, 0.568);
        }
        &::placeholder {
          color: rgba(0,0,0,.54);
        }

        &:-ms-input-placeholder {
          color: rgba(0,0,0,.54);
        }

        &::-ms-input-placeholder {
          color: rgba(0,0,0,.54);
        }
      }
    }
      
    &__content {
      border-radius: 5px;
      font-size: 14px;
      font-weight: normal;
      border: none;
      border-collapse: collapse;
      width: 100%;
      max-width: 100%;
      background-color: white;

      & th, td {
        text-align: left;
        padding: 8px;
        height: 40px;
      }
      
      & thead th {
        color: #ffffff;
        background: rgb(134, 175, 223);
        white-space: nowrap;
      }

      & tbody tr {
        color: #2029af;

        &:nth-child(even) {
          background-color: #cccccc;
        }
        &:nth-child(odd) {
          background-color:white;
        }
        &:hover {
          background-color: rgb(238, 230, 158);
          cursor: pointer;
        }
      }
    }

    .active {
      background-color:  #2029af !important;
      color: white;
    }

    .no-data {
      font-size: 16px;
      background-color:rgb(247, 244, 244) !important;
      color:rgb(27, 24, 24);
        
      & td {
        text-align: center;
      }
    }
  }

  .header {
    font-size: 22px;
    margin: 25px 0;
  }

  .filter {
    display: flex;

    > div {
      margin-right: 30px;
    }
  }
       
  .button {
    padding: 10px;
    border-radius: 3px;
    color:white;
    cursor: pointer;
    min-width: 90px;
    outline:none;

    &__close {
      background-color: cornflowerblue;

      &:hover {
        background-color: rgb(50, 117, 243);
      }
    }

    &__success {
      background-color:#4caf50;

      &:hover:not(:disabled) {
        background-color: #408a42;
      }
    }

    &__delete {
      background-color:crimson;
      float: right;

      &:hover:not(:disabled) {
        background-color:brown;
      }
    }

    &__filter {
      background-color:#6b7fd8;
          
      &:hover:not(:disabled) {
        background-color: #5b59b3;
      }
    }

    &__change-table {
      background-color: #fb8c00;
      font-size: 12px;
      padding: 6px;

      &:hover {
        background-color:#ca7308;
      }
    }
  }

  button:disabled {
    background-color: #cccccc;
    color: #666666;
  }

  .checkbox {
    height: 17px;
    width: 17px;
  }
</style>