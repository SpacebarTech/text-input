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

	props : {
		startingValue : {
			default : () => ''
		},
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

		textarea {
			width: 100%;
			background: transparent;
			border: none;
			outline: none;
			padding: 0;
		}
	}
}
</style>
