---
layout: post
title:      "React-Redux. Easy life search bar."
date:       2021-02-01 03:56:04 +0000
permalink:  react-redux_easy_life_search_bar
---


Before I started my project, I knew I needed a life search that would show and update results based on a query. The Search bar was the first thing I mounted in the App Header. 
I’ve returned to it weeks later, at the very end and realized that having a Redux I needed to build my search functionality there. And I started from creating a search Reducer:

```
export default function searchReducer(state = {query: ""}, action) {
    switch (action.type) {
        case("UPDATE_SEARCH_QUERY"):
            return{...state, query: action.value};
        default:
            return state;
    }
}
```
An action object creator function followed next:
```
export const updateSearchQuery = (value) => {
    return dispatch => {
        dispatch({type: "UPDATE_SEARCH_QUERY", value})
    }
}
```
To combine all the pieces I’ve imported updateSearchQuery function and connect to the search component:
```
import React from 'react'
import { connect } from 'react-redux';
import { updateSearchQuery } from '../actions/search';

const Search = (props) => {
    return (
        <React.Fragment>
            <input type="text" placeholder="Search..." id="name" value={props.searchQuery} onChange={(event) => props.updateSearchQuery(event.target.value)}/>
        </React.Fragment>
    )
}

const mapStateToProps = state => {
    return {
      searchQuery: state.searchReducer.query,
    }
}

export default connect(mapStateToProps, { updateSearchQuery })(Search);
```
The very last thing was to filter the data and show what the user needed:
```
. . . 
const Cocktails = (props) => {
    const {cocktails, searchQuery} = props;
    const filteredCocktails = cocktails.filter(cocktail => searchQuery === "" || cocktail.name.toUpperCase().includes(searchQuery.toUpperCase())
)

    return (
        <React.Fragment>
            {props.loading ? <h3>Loading...</h3> : (filteredCocktails.length === 0 ? <h3>No match!</h3> : filteredCocktails.map(cocktail => <CocktailCard key={cocktail.id} cocktail={cocktail}></CocktailCard>))}
        </React.Fragment>
    )
}
. . .
```
Everything worked great, and just by entering one letter, the App showed all the cocktails having it in their names. Adding another one would narrow down the search and bring the user to the desired goal.

P.S. Later, I've upgraded the App by adding a function for clearing my search query and used it in the event listener on every Home link in the Header and the Footer, so users clicking on them could see the initial page with all the variety of cocktails.  



