<template>
  <li>
    <label>
        <input type="checkbox" :checked='todo.done' @change="handleCheck(todo.id)"/>
            <span v-show="!todo.isEdit">{{todo.title}}</span>
            <input ref="inputTitle" v-show="todo.isEdit" type="text" :value="todo.title" @blur='handlerBlur(todo,$event)'/>
    </label>
    
    <button class="btn btn-danger" @click="handleDelete(todo.id)">删除</button>
    <button class="btn btn-edit" v-show="!todo.isEdit" @click="handleEdit(todo)">编辑</button>
   </li>
</template>

<script>
  import pubsub from 'pubsub-js'
export default {
    name:'MyItem',
    //声明接收对象
    props:['todo'],
    methods:{
        handleCheck(id){
            //通知App将对应done值取反
            // this.checkTodo(id)
            this.$bus.$emit('checkTodo',id)
        },
        //删除
        handleDelete(id){
          if(confirm('你确认？')){
            // this.deleteTodo(id)
            pubsub.publish('deleteTodo', id)
          }
        },
        handleEdit(todo){
          if(todo.hasOwnProperty('isEdit')){
            // console.log('@')
            todo.isEdit = true
          }else{
            this.$set(todo,'isEdit',true)
          }
          this.$nextTick(function(){
            this.$refs.inputTitle.focus()
          })
        },
        handlerBlur(todo,e){
          todo.isEdit = false
          if(!e.target.value.trim()) return alert('输入不能为空')
          this.$bus.$emit('updateTodo',todo.id,e.target.value)
        }
    },
   

}
</script>

<style scoped>
/*item*/
li {
  list-style: none;
  height: 36px;
  line-height: 36px;
  padding: 0 5px;
  border-bottom: 1px solid #ddd;
}

li label {
  float: left;
  cursor: pointer;
}

li label li input {
  vertical-align: middle;
  margin-right: 6px;
  position: relative;
  top: -1px;
}

li button {
  float: right;
  display: none;
  margin-top: 3px;
}

li:before {
  content: initial;
}

li:last-child {
  border-bottom: none;
}
li:hover{
  background-color: #ddd;
}
li:hover button{
  display: block;
}
</style>