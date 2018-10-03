<template lang='pug'>
.text-input(:class='{\
	"empty"      : value === "",\
	"show-label" : ( focused && value !== "" ),\
	"has-error"  : ( displayError !== "" ),\
	focused,\
}')
	.header(v-if='options.label')
		span.label.noselect {{ options.label }}
		span.text-error(v-if='typeof displayError !== "object" && displayError !== ""') {{ displayError }}
	.text-wrapper
		textarea(
			ref='textarea'
			v-model='localValue'
			:placeholder='options.placeholder'
			:class='options.classList'

			@input='resizeInput'
			@keydown.enter.exact='emit( "enter", $event )'
			@focus='emit( "focus", $event )'
		)
</template>

<script>
export default {
	name : 'text-input',

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
	},

	data : () => ( {
		focused      : false,
		localValue   : '',
		displayError : '',
	} ),

	created() {
		this.localValue = this.value ? this.value : '';
	},

	mounted() {
		this.$nextTick( () => {
			this.resizeInput();
		} );
	},

	watch : {

		errors( e ) {
			this.displayError = e;
		},

		localValue( value ) {
			this.displayError = '';

			if ( value.length - this.value.length > 1 ) {
				return;
			}

			this.$emit( 'input', value );
		},

		value( val ) {

			// without this line, you trigger an infinite loop
			// of emitting, hearing a change, emitting, hearing a change,
			// forever
			if ( val !== this.localValue ) {
				this.localValue = val;

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

			let height = Math.floor( textarea.getBoundingClientRect().height );

			while ( height === textarea.scrollHeight ) {

				height -= 5;
				textarea.style.height = `${height}px`;

			}

			textarea.style.height = `${textarea.scrollHeight}px`;

		},

		emit( eventType, event = null ) {
			this.$emit( eventType, event );
		}

	}

};
</script>

<style lang='scss'>

$label : #68A65E;
$error : #e64343 !default;

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
		color: $label;
	}

	.text-error {
		color: $error;

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
