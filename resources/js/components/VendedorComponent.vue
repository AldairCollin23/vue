<template>
<div>
  <div class="row">
    <div class="col">
    <label>DNI</label>
    <input v-model="dni" type="text" placeholder="DNI" class="form-control">
    </div>
    <div class="col">
    <label>Apellido y Nombre</label>
    <input v-model="nombrevendedor" type="text" placeholder="Apellido y Nombre" class="form-control">
    </div>
    <div class="col">
    <label>Celular</label>
    <input v-model="celular" type="text" class="form-control">
    </div>
     <div class="col-md-2">
    <label>Email</label>
    <input v-model="email" type="text" class="form-control">
    </div>
<div class="col-md-12">
    <br>
        <div class="container-buttons">
    <button  class="btn btn-outline-primary" @click="buscar()">Buscar</button>
                    <button v-if="update == 0" @click="saveTasks()" class="btn btn-outline-success">Añadir</button>
                    <!-- Botón que modifica la tarea que anteriormente hemos seleccionado, solo se muestra si la variable update es diferente a 0-->
                    <button v-if="update != 0" @click="updateTasks()" class="btn btn-outline-warning">Actualizar</button>
                    <!-- Botón que limpia el formulario y inicializa la variable a 0, solo se muestra si la variable update es diferente a 0-->
                    <button v-if="update != 0" @click="clearFields()" class="btn btn-outline-danger">Atrás</button>
        </div>
</div>
            <div class="col-md-12">
                <br>
                <h2>Vendedor</h2>
                <table class="table text-center"><!--Creamos una tabla que mostrará todas las tareas-->
                        <thead>
                            <tr>
                                <th scope="col">DNI</th>
                                <th scope="col">Apellido y Nombre</th>
                                 <th scope="col">Celular</th>
                                  <th scope="col">Email</th>
                                <th scope="col">Acciones</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="task in arrayTasks" :key="task.id"> <!--Recorremos el array y cargamos nuestra tabla-->
                                 <td v-text="task.dni"></td>
                                <td v-text="task.nombrevendedor"></td>
                                 <td v-text="task.celular"></td>
                                 <td v-text="task.email"></td>
                                <td>
                                  <button class="btn btn-outline-info btn-sm" @click="loadFieldsUpdate(task)">Modificar</button>
                                    <!--Botón que borra la tarea que seleccionemos-->
                                    <button class="btn btn-outline-danger btn-sm" @click="deleteTask(task)">Borrar</button>
                                </td>
                            </tr>
                        </tbody>
                    </table>
            </div>

  </div>
</div>






</template>
<script>
    export default {
        data(){
            return{
                nombrevendedor:"", //Esta variable, mediante v-model esta relacionada con el input del formulario
                celular:"",
                dni:"",
                email:"",
                
                update:0, /*Esta variable contrarolará cuando es una nueva tarea o una modificación, si es 0 significará que no hemos seleccionado
                          ninguna tarea, pero si es diferente de 0 entonces tendrá el id de la tarea y no mostrará el boton guardar sino el modificar*/
                arrayTasks:[], //Este array contendrá las tareas de nuestra bd
            }
        },
        methods:{
  buscar(){
            const TOKEN ='6blZbXM4yVjPqDmSdis6UwUq7p7okqZsXNMPukGvMIq2IwjQw5L9zMUHvhNg'
            const BASEURL = 'https://api.migo.pe/api/v1/dni';
            const ENDPOINT = 'dni';
            const dniB = { dni: this.dni };
            axios.create({
            baseURL: BASEURL,
            headers: {
            'Content-Type': 'application/json',
            'Authorization': 'Bearer '+TOKEN

            }

            })

            .post(ENDPOINT,dniB)

            .then(res => {

            //ira la modificacion de los datos
         
          this.nombrevendedor=res.data.nombre_completo;
          

          //this.txtcelular="Ira el celular";

         // this.txtemail="Ira el email";
            console.log(res.data);

            });

            //alert("Buscar:"+this.dni);

            },


            getTasks(){
                let me =this;
                let url = '/vendedor' //Ruta que hemos creado para que nos devuelva todas las nombre
                axios.get(url).then(function (response) {
                    //creamos un array y guardamos el contenido que nos devuelve el response
                    me.arrayTasks = response.data;
                })
                .catch(function (error) {
                    // handle error
                    console.log(error);
                });
            },
            saveTasks(){
                let me =this;
                let url = '/vendedor/guardar' //Ruta que hemos creado para enviar una tarea y guardarla
                axios.post(url,{ //estas variables son las que enviaremos para que crear la tarea
                    'nombrevendedor':this.nombrevendedor,
                    'celular':this.celular,
                    'dni':this.dni,
                    'email':this.email,
                   

                }).then(function (response) {
                    me.getTasks();//llamamos al metodo getTask(); para que refresque nuestro array y muestro los nuevos datos
                    me.clearFields();//Limpiamos los campos e inicializamos la variable update a 0
                })
                .catch(function (error) {
                    console.log(error);
                });   

            },
            updateTasks(){/*Esta funcion, es igual que la anterior, solo que tambien envia la variable update que contiene el id de la
                tarea que queremos modificar*/
                let me = this;
                axios.put('/vendedor/actualizar',{
                    'idvendedor':this.update,
                    'nombrevendedor':this.nombrevendedor,
                    'nombre':this.nombre,
                    'dni':this.dni,
                    'celular':this.celular,
                    

                }).then(function (response) {
                   me.getTasks();//llamamos al metodo getTask(); para que refresque nuestro array y muestro los nuevos datos
                   me.clearFields();//Limpiamos los campos e inicializamos la variable update a 0
                })
                .catch(function (error) {
                    console.log(error);
                });
            },
            loadFieldsUpdate(data){ //Esta función rellena los campos y la variable update, con la tarea que queremos modificar
                this.update = data.idvendedor
                let me =this;
                let url = '/vendedor/buscar?idvendedor='+this.update;
                axios.get(url).then(function (response) {
                    me.nombrevendedor= response.data.nombrevendedor;
                    me.celular= response.data.celular;
                    me.dni= response.data.dni;
                    me.email= response.data.email;
                  

                })
                .catch(function (error) {
                    // handle error
                    console.log(error);
                }); 
            },
            deleteTask(data){//Esta nos abrirá un alert de javascript y si aceptamos borrará la tarea que hemos elegidvendedoro
                let me =this;
                let task_id = data.idvendedor
                if (confirm('¿Seguro que deseas borrar esta tarea?')) {
                    axios.delete('/vendedor/borrar/'+task_id
                    ).then(function (response) {
                        me.getTasks();
                    })
                    .catch(function (error) {
                        console.log(error);
                    }); 
                }
            },
            clearFields(){/*Limpia los campos e inicializa la variable update a 0*/
                this.nombrevendedor = "";
                this.celular = "";
                this.dni = "";
                this.email = "";
          

                this.update = 0;
            }
        },
        mounted() {
           this.getTasks();
        }
    }
</script>

