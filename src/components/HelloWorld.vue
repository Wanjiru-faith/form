<template>
<div id ="form">
<h1>Form Validation</h1>
<form @submit.prevent="submitForm" autocomplete="off">

<div class="form-group">
<label for="name">Name:</label>
<input v-model="form.name"
@blur="$v.form.name.$touch()" 
:class ="{error: shouldAppendErrorClass($v.form.name), valid: shouldAppendValidClass($v.form.name)}"
id="name">
<!--input event handler:@input = "$v.form.name.$touch()"
$error === $invalid && $dirty
$invalid  && $v.form.name.$invalid"
-->
<p v-if="$v.form.name.$error" class="error-message">You have not entered your name</p>
<p>Invalid: {{$v.form.name.$invalid}} | Dirty: {{$v.form.name.$dirty}}</p>

</div>
 
<div class="form-group">
<label for="age">Age:</label>
<!--age is a string by default thus the invalid message-->
<input v-model.number="form.age"
@blur="$v.form.age.$touch()"   
:class ="{error: shouldAppendErrorClass($v.form.age), valid: shouldAppendValidClass($v.form.age)}"
id="age">

  <div v-if="$v.form.age.$error">
    <p v-if ="!$v.form.age.required" class="error-message">You have not entered your age</p>
    <p v-else-if ="!$v.form.age.integer" class="error-message">Your age should be a whole number</p>
    <p v-else-if ="!$v.form.age.between" class="error-message">Your age should be above 12 and below 121</p>
  </div>

</div> 

<div class ="form-group">
<label for="email">Email:</label>
<input v-model="form.email"
  @blur="$v.form.email.$touch()" 
  :class ="{error: shouldAppendErrorClass($v.form.email), valid: shouldAppendValidClass($v.form.email)}"
  id="name">
  <p v-if="!$v.form.email.email && $v.form.email.$error" class="error-message">Invalid email adress</p>
  <p v-if="!$v.form.email.required && $v.form.email.$error" class="error-message">Email is required so we can send you the newsletter</p>
</div>

<div class="form-group">
      <label for="newsletter">Subscribe to the newsletter:</label>
      <input v-model="form.newsletter"
             type="checkbox"
             id="newsletter">
             </div>

<div class="form-group github-username">
      <label for="github-username">GitHub username:</label>
      <input v-model.lazy="$v.form.githubUsername.$model"
             :class="{error: shouldAppendErrorClass($v.form.githubUsername), valid: shouldAppendValidClass($v.form.githubUsername)}"
             id="github-username"
      >
      <span v-show="$v.form.githubUsername.$pending" class="fa fa-spinner fa-spin"></span>
      <p v-if="!$v.form.githubUsername.exists && $v.form.githubUsername.$error" class="error-message">There is not GitHub user with this username</p>
    </div>

    <div class="form-group">
      <label for="food">Pizza Or Burger:</label>
      <input v-model.number="form.food"
      @blur="$v.form.food.$touch()"
      :class="{error: shouldAppendErrorClass($v.form.food), valid: shouldAppendValidClass($v.form.food)}"
      id="food">
      <p v-if="$v.form.food.$error && !$v.form.food.pizzaOrBurger" class ="error-message">Sorry! We only serve burgers and pizzas</p>
      </div>


<div class="form-group">
<button id="submit-button" :disabled="$v.form.$invalid">Submit</button>
</div>

</form>
</div>
</template>

<script>
import { required, integer, between,email } from 'vuelidate/lib/validators'

export default {
  data(){
    return{
      form:{
        name:null,
        age:null,
        email:null,
        githubUsername:null,
        newsletter:null,
        food: null
      }
    }
  },
  validations:{
    form:{
      name:{
        required
      },
      age:{
        required,
        integer,
        between: between(12, 120)
      },
      email:{
        email,
        required: /*validators requiredIf*/(function(){
          return !!this.form.newsletter
        })
      },
      food:{
        pizzaOrBurger: value => value === 'pizza' || value === 'burger'|| !value
        //!validators.helpers.req(value)
      },
      githubUsername:{
        exists(value){
          //if(!validators.helpers.req(value)){
            if(!value){
            return true
          }
          return axios.get('//api.github.com/users/${value}')
          // return new Promise((resolve, reject) =>{
          //   setTimeout(() => reject(value.length>3), 1000)
          }
        }
      }      
    },    

  methods:{
    shouldAppendValidClass(field){
      return !field.$invalid && field.$model && field.$dirty
    },
    shouldAppendErrorClass(field){
      return field.$error
    },
    submitForm(){
    if(!this.$v.form.$invalid){ 
      console.log('Form Submitted', this.form)
    }else{
      console.log('Invalid form')
     } 
    } 
  }
}
//<script src="https://cdnjs.cloudflare.com/ajax/libs/axios/0.18.0/axios.min.js"></script>
</script>

<style scoped lang="scss">

.error-message{
  color:red;
}
#form {
    // display: inline-block;
    // width: 49%;
    align-items:center;
    text-align: center;
  }
  .validators {
    display: inline-block;
    width: 49%;
    text-align: center;
    vertical-align: top;
  }
  .form-group{
    padding: 5px;
  }
  #submit-button{
    color:grey;
    background:purple;
    outline:none;
    border-radius:3px;
  }
  #submit-button:hover{
    cursor:pointer;

  }

</style>
