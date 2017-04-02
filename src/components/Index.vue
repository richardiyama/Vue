<template>
  <q-layout>
    <div slot="header" class="toolbar">
      <q-toolbar-title :padding="1">
       Orb Solution Todo List
      </q-toolbar-title>
      <button @click="add">
        <i>note_add</i>
      </button>
    </div>

    <div class="layout-view">

      <q-search v-model="searchModel"></q-search>
      <div>{{recCount}}</div>
      <div class="list">
        <div v-for="(item, index) in filteredTasks()" class="item">

          <i class="item-primary">lightbulb_outline</i>
          <div class="item-content has-secondary">
            <div>{{item.label}}</div>
          </div>

          
          <div class="item-secondary">
            <i slot="target">
              more_vert
              <q-popover  :ref="'popover'">
                <div class="list">

                  <div class="item item-link" @click="edit(index)">
                    <div class="item-content">Edit</div>
                  </div>
                  <div class="item item-link" @click="deleteItem(index)">
                    <div class="item-content">Delete</div>
                  </div>
                </div>
              </q-popover>
            </i>
          </div>



        </div>
      
      </div>
    </div>
  </q-layout>
</template>

<script>
  import _ from 'lodash'
  import Quasar, { Utils, Dialog, LocalStorage,SessionStorage } from 'quasar'

  export default {
    data() {
      return {
        searchModel: "",
        recCount: 0,
        
        
      }
    },

    
    methods: {
      filteredTasks() {
        var tasks = LocalStorage.get.item('taskList',[])
        if (this.searchModel.length > 0) {
          console.log("hello")
          retv = _.filter(tasks, {label: this.searchModel})
          this.recCount = retv.length

          return retv
        }

        console.log("yello")

        this.recCount = tasks.length
        return tasks
      },

      add() {
        var self = this
        Dialog.create({
          title: 'New task',
          form: {
            task: {
              type: 'textbox',
              label: 'My task',
              model: ''
            }
          },
         
          buttons: [
            'Delete',
            {
              label: 'Create',
              handler(data) {
                console.log(data)
                var newTask =  [];
                

                // var tasks = LocalStorage.get.item('taskList')
                 //console.log(tasks)
                   newTask.push({
                    label: data.task
                    
                  })
               
                LocalStorage.set('taskList', newTask)
                
                self.recCount =+1
              }
            }
          ]
        })
      },
       
    
    edit(index) {
     this.$refs.popover[0].close(index);
     LocalStorage.get.item(index);
        var self = this
        Dialog.create({
          title: 'New task',
          form: {
            task: {
              type: 'textbox',
              label: 'Edit Task',
              model: index
            }
          },
         
          buttons: [
            'Delete',
            {
              label: 'Create',
              handler(data) {
                console.log(data)
                
                console.log(index)
                var editTask =  [];
              

                // var tasks = LocalStorage.get.item('taskList')
                 //console.log(tasks)
                   editTask.push({
                    label: data.task
                    
                  })
               
                LocalStorage.set('taskList', editTask)
              
                self.recCount += 1
              }
            }
          ]
        })
      },
       
    

     deleteItem(item) {
              
               var self = this
              this.$refs.popover[0].close(item);
              LocalStorage.get.item(item);
              console.log(item)
              LocalStorage.remove(item)
              LocalStorage.set('taskList',item)
            
                self.recCount += 1
            }
    },

    mounted() {
      // create task store if not exist
      if (LocalStorage.has('taskList') === false) {
        LocalStorage.set('taskList', [])
      }
    }
  }
  
</script>