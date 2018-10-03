Text Input
========

Text input component

## Installation

  `npm install @spacebartech/text-input`

## Usage

  ```
  props : {
		options : {
			type    : Object,
			default : () => ( {} )
		},
		errors : {
      type    : String,
			default : () => ''
		},
		value : {
      type    : String,
			default : () => '',
		},
  }

  Options must have the following props :
  label       : String,
  placeholder : String,
  classList   : String

  text-input(
    :options="options"
    :errors="'Error String'"
    @value='value'
    @change='' // emits when inner textarea has changed
  )


  ```
## Tests

`npm test`
