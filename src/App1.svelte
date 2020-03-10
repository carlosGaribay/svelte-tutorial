<script>
	import { setContext } from "svelte";
	
	//components
	import Navbar from './Navbar.svelte';
	import ExpensesList from './ExpensesList.svelte';
	import Totals from './Totals.svelte';
	import ExpenseForm from './ExpenseForm.svelte';
	//data
	import expensesData from './expenses';
	//variables
	let expenses = [...expensesData];
	//set editing variables
	let setName = "";
	let setAmount = null;
	let setId = null;
	//reactive
	$: isEditing = setId ? true : false;
	$: total = expenses.reduce((acc, curr) => {
		return (acc += curr.amount)
	}, 0);
	//remove expense
	function removeExpense(id){
		expenses = expenses.filter(item => item.id !== id);
	}
	//context
	setContext("remove", removeExpense);
	setContext("modify", setModifiedExpense);
	//add expense
	function addExpense() {
		let expense = { id: Math.random() * Date.now(), name:setName, amount:setAmount };
		expenses = [expense, ...expenses];
	}
	//setModifiedExpense
	function setModifiedExpense(id) {
		let expense = expenses.find(item => item.id === id);
		setId = expense.id;
		setName = expense.name;
		setAmount = expense.amount;
	}
	function editExpense() {
		expenses = expenses.map(item => {
			return item.id === setId ? {...item, name:setName, amount:setAmount} : {...item}
		});
	}
	//clear Expenses
	function clearExpenses(){
		expenses = [];
	}
	function handleSubmit() {
		if(isEditing){
			editExpense();
		}else{
			addExpense();
		}
		setId = null;
		setAmount = null;
		setName = "";
	}
	$:console.log({setName, setAmount});
</script>

<Navbar />
<main class="content">

	<ExpenseForm 
		bind:name={setName}
		bind:amount={setAmount}
		{handleSubmit}
		{isEditing}  />
	<Totals title="total expenses" {total} />
	<ExpensesList {expenses} />
	<button 
		type="button" 
		class="btn btn-primary btn-block" 
		on:click={clearExpenses}>
		clear expenses
	</button>
</main>