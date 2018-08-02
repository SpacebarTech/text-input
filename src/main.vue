<template lang='pug'>
.text-input(:class='{\
	"empty"      : value === "",\
	"show-label" : ( focused && value !== "" ),\
	"has-error"  : ( displayError !== "" ),\
	focused,\
}')
	.header
		span.label.noselect {{ options.label }}
		span.text-error(v-if='typeof displayError !== "object" && displayError !== ""') {{ displayError }}
	.text-wrapper
		textarea(
			ref='textarea'
			v-model='localValue'
			:placeholder='options.placeholder'
			:class='options.classList'

			@input='resizeInput'
		)
</template>

<script>
export default {

	template : '#text-input',

	props : {
		startingValue : {
			default : () => ''
		},
		// placeholder : {
		// 	default : () => 'Type here to input text'
		// },
		// label : {
		// 	default : () => 'input'
		// },
		// classList : {
		// 	default : () => ''
		// },
		options : {
			type : Object,
		},
		errors : {
			default : () => ''
		},
		value : {
			default : () => '',
		},
	},

	data : () => ( {
		focused      : false,
		localValue   : '',
		displayError : '',
	} ),

	created() {
		this.localValue = this.initialValue ? this.initialValue : '';
	},

	mounted() {
		this.$nextTick( () => this.resizeInput() );
	},

	watch : {

		errors( e ) {
			this.displayError = e;
		},

		localValue( value ) {
			this.displayError = '';
			this.$emit( 'input', value );
		},

		initialValue( val ) {

			// without this line, you trigger an infinite loop
			// of emitting, hearing a change, emitting, hearing a change,
			// forever
			if ( val !== this.value ) {
				this.value = val;

				this.$nextTick( () => this.resizeInput() );
			}

		}

	},

	methods : {

		resizeInput() {

			const { textarea } = this.$refs;

			this.$emit( 'change', textarea );

			if ( !textarea ) {
				return;
			}

			this.$emit( 'change', textarea );

			const height = textarea.style.height;

			let heightNumeric = parseInt( height.substr( 0, height.length - 2 ), 10 );

			while ( heightNumeric === textarea.scrollHeight ) {

				heightNumeric -= 5;
				textarea.style.height = `${heightNumeric}px`;

			}

			textarea.style.height = `${textarea.scrollHeight}px`;

		},

	}

};
</script>

<style lang='scss'>

$green              : #68A65E;
$red                : #e23434;
$secondaryGrey      : #323232;
$secondaryBoxShadow : 0px 1px 4px rgba(0,0,0,0.08), 0px 1px 2px rgba(0,0,0,0.12);

.text-input {
	position: relative;

	.header {
		display: inline-block;
		font-size: 16px;
		padding-left: 25px;
		margin-bottom: 5px;
		text-transform: uppercase;
		letter-spacing: 2px;

		span {
			display: inline;
		}
	}

	.label {
		color: $green;
	}

	.text-error {
		color: $red;

		&::before {
			content : ' - ';
		}
	}

	.text-wrapper {
		padding: 15px;
		margin: 0 15px;
		background: $secondaryGrey;
		border-radius: 10px;
		box-shadow: $secondaryBoxShadow;

		textarea {
			width: 100%;
			background: transparent;
			color: white;
		}
	}
}
</style>
