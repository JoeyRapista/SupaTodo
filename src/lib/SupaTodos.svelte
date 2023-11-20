<script>
  import { onMount } from "svelte";
  import { supabase } from "../supabaseClient";

  let tasks = [];
  let task = "";
  let isDone;
  let isLoading = false;

  const fetchTasks = async () => {
      try {
        isLoading = true
        let { data, error } = await supabase
        .from('tasks')
        .select('*')

        if (error){
            console.log(error)
        }else{ 
            tasks = [...data] 
        }        
      } catch (error) {
        console.log(error.message)
      }finally{
        isLoading = false
        
      } 
  } 
  const deleteTask = async (id) => {
    try {
      isLoading = true;
      const taskToDelete = tasks.filter(task=>(task.id === id))[0]
      
      if (!taskToDelete){
        return;
      }
  
      const { error } = await supabase
        .from('tasks')
        .delete()
        .eq('id', taskToDelete.id)
          
      
      if (error) {
        console.log(error.message);
      } else {
        tasks = tasks.filter((task) =>  task.id !== id );
      }
    } catch (error) {
      console.log(error.message)
    }finally{
      isLoading = false;
    }
          
  }
  const addTask = async(e) => { 
    if(e.key === 'Enter'){ 

      try {
        isLoading = true
        const { data, error } = await supabase
        .from('tasks')
        .insert([
          { title: task },
        ])
        .select()


        if (error){
          console.log(error.message)
        }else{
          tasks = [...tasks, data[0]]
        }
      } catch (error) {
        
      }finally{
        isLoading = false
        task = ""
      }
      
    }
          
  }
  onMount(async()=>{
      await fetchTasks()
  })
</script> 
<div>  
  <h3>Super Duper Todos</h3> 
  <input bind:value={task} on:keydown={addTask}  type="text">
 
  <ul>
    {#each tasks as task (task.id)}
      <li>{task.title} <button on:click={()=>deleteTask(task.id)}>Del</button> </li>
    {/each}
  </ul>
</div>