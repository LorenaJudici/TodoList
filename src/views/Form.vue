<template>
  <div class="container mt-2" >
      <b-form>
          <b-form-group
            label="Titulo"
            label-for="subject"
          >
            <b-form-input
                id="subject"
                v-model.trim="$v.form.subject.$model"
                type="text"
                placeholder="Ex:Lavar Carro"
                required
                autocomplete="off"
                :state="getValidation"
                area-describeby = "subject-feedback"
            >   
            </b-form-input>   

            <b-form-invalid-feedback id="subject-feedback">Campo obrigatório.</b-form-invalid-feedback>
          </b-form-group>

          <b-form-group
            label="Descrição"
            label-for="description"
          >
            <b-form-textarea
                id="description"
                v-model="form.description" 
                type="text"
                placeholder="Ex:precisa levar o carro para lavar"
                required
                autocomplete="off"
            >     
          </b-form-textarea>  
          </b-form-group>

          <b-form-group
            label="Status"
            label-for="status"
          >
            <b-form-select
                id="status"
                v-model="form.status" 
                :options="optionsList"
            >     
          </b-form-select>  
          </b-form-group>

          <b-form-group
            label="Data de vencimento"
            label-for="dateOverdue"
          >
            <b-form-datepicker
                id="dateOverdue"
                v-model="form.dateOverdue"
                label-no-date-selected="Selecione uma data"
                :min="getToday()"
            >

            </b-form-datepicker>
          </b-form-group>


          <b-button 
          type="submit" 
          variant="outline-primary" 
          @click="saveTask"
          >
              
              <i class="fa-solid fa-save"></i>
              Salvar
          </b-button>
      </b-form>
  </div>
</template>


<script>
import ToastMixin from "@/mixins/toastMixin.js";
import {required, minLength} from "vuelidate/lib/validators"
import TasksModel from '@/models/TasksModel';
import Status from "@/valueObjects/status";
export default {
    name:"Form",
    mixins:[ToastMixin],

    data()  {
        return{
            form:{
                subject:"",
                description:"",
                status: Status.OPEN,   
                dateOverdue:"",   
                userId: JSON.parse(localStorage.getItem('authUser')).id // pega o id do usuário logado      
            },
            methodSave: "new",
            optionsList:[
                {value:Status.OPEN,text:"Aberto"},
                {value:Status.FINISHED,text:"Concluido"},
                {value:Status.ARCHIVED,text:"Arquivado"}
            ]
        }
    },

    validations:{
        form:{
            subject:{
                required,
                minLength: minLength(3)
            }
        }
    },

    async created(){ //Verifica se o cliente clicou no alterar para assim trazer os dados em tela preenchidos
    //Quando quiser usar localbase fica if(this.$route.params.index == 0 || this.$route.params.index !== undefined)
        if(this.$route.params.taskId){
            this.methodSave = "update"; // Usa igual para a api e para localbase
            this.form = await TasksModel.find(this.$route.params.taskId);
            //Quando for usar localbase
            //let tasks = JSON.parse(localStorage.getItem("tasks"));
            //this.form = tasks[this.$route.params.index];
        }
    },

    methods: {
        saveTask(){ 

            this.$v.$touch();
            if(this.$v.$error) return;

            if(this.methodSave === "update"){ //Se for editar

                this.form.save();
                //Quando for usar localbase
                // let tasks = JSON.parse(localStorage.getItem("tasks"));
                // tasks[this.$route.params.index] = this.form;
                // localStorage.setItem("tasks", JSON.stringify(tasks));

                this.showToast("sucess" , "Sucesso", "Tarefa atualizada com sucesso");
                this.$router.push({name: "list"});
                return; //Não pode executar parte de baixo
            }

            //Usado para o localStorage
            // let tasks = (localStorage.getItem("tasks"))? JSON.parse(localStorage.getItem("tasks")) : [];
            // tasks.push(this.form);
            // localStorage.setItem("tasks", JSON.stringify(tasks));

            //Usado para a API 
            const task = new  TasksModel(this.form);
            task.save();

            this.showToast("sucess" , "Sucesso", "Tarefa criada com sucesso");
            this.$router.push({name: "list"});
        },

        getToday(){
            return new Date().toISOString().split("T")[0];
        }
    },

    computed:{
        getValidation(){
            if(this.$v.form.subject.$dirty === false){//se o campo não foi mexido para disparar o vermelho
                return null;
            } 
            return ! this.$v.form.subject.$error; //se for mexido
        }
    }
}
</script>
