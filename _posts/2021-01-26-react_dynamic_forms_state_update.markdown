---
layout: post
title:      "React. Dynamic Forms. State Update. "
date:       2021-01-26 06:57:17 +0000
permalink:  react_dynamic_forms_state_update
---


Working on my final project I faced the challenge of updating a state in dynamic forms. 
Let me break it into small pieces. I needed a form for creating a cocktail, which had dynamic inputs for ingredients and instructions. To do that I build a class component CocktailForm which was rendering inputs for :name, :description, :imgUrl as well as two function components: InstructionForm and IngredientForm.  
My initial state looked like this: 
```
    state = {
        name: "",
        description: "",
        imgUrl: "",
        instructions: [""],
        ingredients: [
            {
                name: "",
                quantity: "",
            }
        ]
    }
```
But in reality, it could have five steps of preparation and ten ingredients.
I started with the easiest part, where I could update the state of the :name, : description and :imgUrl :
```
    handleChange = event => {
        this.setState({
            ...this.state,
            [event.target.name]: event.target.value
        })
    }
```
As instructions were unique for each cocktail and I could not use them in the different cocktails, I decided to store them as an array in my database. So, I needed a method that would iterate over several input fields and return the array of instructions:
```
    handleChangeInstruction = (index, value) => {
        this.setState({
            ...this.state,
            instructions: [
                ...this.state.instructions.slice(0, index),
                value, 
                ...this.state.instructions.slice(index + 1),
            ]
        })
    }
```
Also, I passed this method as props into InstructionForm:
```
render() {
let { instructions } = this.state
return (
<form className="grid-container" onSubmit={this.handleSubmit}>
. . .
	<InstructionForm instructions={instructions} handleChange={this.handleChangeInstruction}/>
	<button type=”button” onClick={this.addInstruction}/>
. . .
      </form>
        )
    }

```
…where I was mapping values of each input using its index to break them apart:
```
  import React from 'react'

const InstructionForm = ({ instructions, handleChange }) => {
    return (
        instructions.map((value, index) => {
            return (
                <div key={index}>
                    <input className="input" type="text" placeholder="Instruction" value={value} onChange={(event) => handleChange(index, event.target.value)}/> 
                </div>
            )
        })
    )
}

export default InstructionForm;
```
IngredientForm was even more challenging because I needed to collect ingredient names along with quantities (measurements). The result had to be an array of hashes (objects).
IngredientForm component was similar to InstructionForm, except it had two inputs: one for the name and another for the quantity. However, I could not come up with a single method for updating name and quantity states and, I had to break it into two pieces.
The first method was updating name inputs: 
```
    handleChangeIngredientName = (index, value) => {
        this.setState({
            ...this.state,
            ingredients: [
                ...this.state.ingredients.slice(0, index),
                {
                    name: value,
                    quantity: this.state.ingredients[index].quantity
                }, 
                ...this.state.ingredients.slice(index + 1),
            ]
        })
    }
```
The second was guarding the quantity inputs:
```
    handleChangeIngredientQuantity = (index, value) => {
        this.setState({
            ...this.state,
            ingredients: [
                ...this.state.ingredients.slice(0, index),
                {
                    name: this.state.ingredients[index].name,
                    quantity: value
                },
                ...this.state.ingredients.slice(index + 1),
            ]
        })
    }
```
Those two methods were passes as props into IngredientForm component:
```
render() {
let { instructions } = this.state
return (
<form className="grid-container" onSubmit={this.handleSubmit}>
. . .
	<IngredientForm ingredients={ingredients} handleChangeName={this.handleChangeIngredientName} handleChangeQuantity={this.handleChangeIngredientQuantity}/>
<button type=”button” onClick={this.addInstruction}/>
. . .
      </form>
        )
    }
```
…which looked like this:
```
import React from 'react';

const IngredientForm = ({ ingredients, handleChangeName, handleChangeQuantity }) => {
    return (
        ingredients.map((value, index) => {
            return (
                <div key={index}>
                    <input className="input" type="text" name={`ingredient-name-${index}`} placeholder="Ingredient" value={value.name} onChange={(event) => handleChangeName(index, event.target.value)}/>
                    -
                    <input className="input" type="text" name={`ingredient-quantity-${index}`} placeholder="Quantity" value={value.quantity} onChange={(event) => handleChangeQuantity(index, event.target.value)}/>
                </div>
            )
        })
    )
}

export default IngredientForm;
```
This pattern was a success, and I was able to get the result I wanted. 
The issue looked unreal to me at the beginning until I started breaking it apart and do small familiar steps. Lesson learned: "there are no big problems, there are just a lot of little problems" that could be solved easily. 


