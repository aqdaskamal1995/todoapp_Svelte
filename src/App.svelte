
<script>
	import { initializeApp, getApp, getApps } from "firebase/app";
	import { getFirestore, collection, onSnapshot, doc, updateDoc, deleteDoc, setDoc } from "firebase/firestore";

	const firebaseConfig = {
    apiKey: "AIzaSyCu29Tcc7-_ed4rkrIpida6rrK2iaVf4Z4",
    authDomain: "cs279-64ca7.firebaseapp.com",
    databaseURL: "https://cs279-64ca7-default-rtdb.firebaseio.com",
    projectId: "cs279-64ca7",
    storageBucket: "cs279-64ca7.appspot.com",
    messagingSenderId: "928427474992",
    appId: "1:928427474992:web:6f753a73dd2c35b56fadf5",
	measurementId: "G-6WHEGV4WYL"}
	
	// Initialize Firebase
	const app = initializeApp(firebaseConfig);


	// Initialize Cloud Firestore and get a reference to the service
	const db = getFirestore(app);

	// Call the relevant documents from firebase 
	const colRef = collection(db, "todoapp")

	console.log({app, db})

	let todos = [];


	// Read data from firebase 
	const unsubscribe = onSnapshot(colRef, (querySnapshot) => {
		let fbTodos = []
		querySnapshot.forEach((doc) => {
			let todo = {...doc.data(), id: doc.id}
			fbTodos = [todo, ...fbTodos];
		});
		console.table(fbTodos);
		todos = fbTodos;
		});

	let task = ""

	
	const addTodo = () => {
		// Takes the existing todo list and appends the latest to do item to it 
		// define a sample to do task with todo and append 
		let todo = {task: task, isComplete:false, task_id: new Date().toISOString()};
		// append to dos list
		todos = [...todos, todo];
		// reset task to be an empty string


		setDoc(doc(db, "todoapp", todo.task_id), 
		{task: task, isComplete: false});
		task = " ";
	}
	function deleteToDo(id){
		// Deletes an item from to do list when clicked 
		/*todos = todos.filter(function( obj ) {
		return obj.task_id !== id;*/
		deleteDoc(doc(db, "todoapp", id));
		};
	
	function taskCompleted(item){
		// Updates the isCompleete attribute if user selects the is complete option 
		// todos[index].isComplete =! todos[index].isComplete};
		updateDoc(doc(db, "todoapp",item.id ),{
			isComplete: !item.isComplete
		})
	};
</script>

<input type ='text' bind:value={task}/>

<button on:click={addTodo}>Add</button>

<ol>
	{#each todos as todo}
	<li class:complete={todo.isComplete}>
		<span>
			{todo.task}
		</span> 
		<span>
			<button on:click={() => taskCompleted(todo)}>✔️</button>
		</span>
		<span>
			<button on:click={() => deleteToDo(todo.id)}>🗑️</button>
		</span>
	
	
</li>
{/each}
</ol>

<style>
	.complete{text-decoration: line-through;}

</style>