<template>
<div class="contactForm">
  <v-alert v-show="formSuccess" icon="done" success>
    {{ $t('form.successMessage') }}
  </v-alert>
  <div v-show="!formSuccess">
    <img class="homer" src="~assets/images/homer.svg" alt="Illustration">
    <h4>{{ title }}</h4>
    <form method="POST" v-on:submit.prevent="onSubmit">
      <v-text-field name="name" :label="$t('form.name')" v-model="name" required :rules="[rules.required]"></v-text-field>
      <v-text-field name="email" :label="$t('form.email')" v-model="email" required :rules="[rules.required, rules.email]"></v-text-field>
      <v-text-field name="message" :label="$t('form.message')" counter v-model="message" max="400" multi-line :required="!attachment" :rules="[rules.required]"></v-text-field>
      <div class="attachment" v-show="attachment">
        <v-icon>attachment</v-icon> {{ $t('form.attachment') }}
      </div>
      <div class="form-action">
        <slot></slot>
        <v-btn type="submit" primary>{{ $t('form.submit') }}</v-btn>
      </div>
    </form>
  </div>
</div>
</template>

<script>
import $ from 'jquery'

export default {
  data() {
    return {
      name: null,
      message: null,
      email: null,
      attachment: this.quote,
      rules: {
        required: (value) => (value === null || !!value) || this.$t('form.error.required'),
        email: (value) => {
          const pattern = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
          return (value === null || pattern.test(value)) || this.$t('form.error.invalidEmail')
        }
      },
      formSuccess: false
    }
  },
  props: {
    title: {
      default: function () {
        return this.$t('form.title')
      }
    },
    subject: {
      type: String,
      default: 'Santihans Inquiry'
    },
    quote: {
      type: Object
    }
  },
  methods: {
    onSubmit: function () {
      var self = this
      $.ajax({
        url: 'https://formspree.io/info@santihans.com',
        method: 'POST',
        data: {
          _subject: self.subject,
          name: self.name,
          email: self.email,
          message: self.message,
          quote: self.quote
        },
        dataType: 'json'
      }).then(function () {
        self.formSuccess = true
        self.$emit('success', true)
      })
    }
  },
  i18n: {
    messages: {
      en: {
        form: {
          title: 'Drop us a line',
          name: 'Name',
          email: 'E-mail',
          message: 'Message',
          submit: 'Submit',
          error: {
            required: 'Required',
            invalidEmail: 'Invalid e-mail'
          },
          attachment: 'Personal Quote',
          successMessage: 'Thank you for your message. We\'ll get back to you as soon as possible.'
        }
      },
      de: {
        form: {
          title: 'Schreib uns',
          name: 'Name',
          email: 'Email',
          message: 'Nachricht',
          submit: 'Abschicken',
          error: {
            required: 'Erforderlich',
            invalidEmail: 'Ungültige Email'
          },
          attachment: 'Persönliche Offerte',
          successMessage: 'Vielen Dank für die Nachricht. Wir melden uns so schnell wie möglich.'
        }
      }
    }
  }
}
</script>

<style lang="scss">
@import "~assets/styles/variables.scss";
.form-action {
    display: flex;
    justify-content: flex-end;
}
.homer {
    width: 70px;
    margin-left: auto;
    margin-bottom: -50px;
    @media(min-width: $breakpointMini) {
        width: 100px;
    }
}
</style>
