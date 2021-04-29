<template>
  <div class="email-display">
    <div>
      <button @click="toggleArchive">
        {{ email.archived ? 'Move to Inbox(e)' : 'Archive(e)' }}
      </button>
      <button @click="toggleRead">
        {{ email.read ? 'Mark Unread(r)' : 'mark Read(r)' }}
      </button>
      <!-- <button @click="goNewer">Newer (k)</button>
      <button @click="goOlder">Older (j)</button> -->
    </div>
    <h2>
      <b>Subject:</b> <strong>{{ email.subject }}</strong>
    </h2>
    <div>
      <b> From:</b><em>{{ email.from }} on {{ email.sentAt }}</em>
    </div>
    <div>
      <!-- {{ email.sentAt }} -->

      {{ format(new Date(email.sentAt), 'MMM do yyyy') }}
    </div>
  </div>
  <div>{{ email.from }}</div>
  <div>{{ email.body }}</div>
</template>

<script>
import useKeydown from '../composables/use-keydown'
import axios from 'axios'
import { format } from 'date-fns'

export default {
  setup(props, { emit }) {
    let email = props.email
    let toggleRead = () => {
      email.read = !email.read
      axios.put(`http://localhost:3000/emails/${email.id},email`)
    }
    let toggleArchive = () => {
      console.log('jhjh')
      email.archived = !email.archived
      axios.put(`http://localhost:3000/emails/${email.id},email`)
    }

    /*   let toggleRead = (email) => {
      email.emit('changeEmail', { toggleRead: true, save: true })
    }
    let toggleArchive = () => {
      emit('changeEmail', { toggleArchive: true, save: true, closeModal: true })
    } */
    // let goNewer = () => {
    //   emit('changeEmail', { changeIndex: -1 })
    // }
    // let goOlder = () => {
    //   emit('changeEmail', { changeIndex: 1 })
    // }
    // let goNewerAndArchive = () => {
    //   emit('changeEmail', { changeIndex: -1, toggleArchive: true, save: true })
    // }
    // let goOlderAndarchive = () => {
    //   emit('changeEmail', { changeIndex: 1, toggleArchive: true, save: true })
    // }

    useKeydown([
      { key: 'r', fn: toggleRead },
      { key: 'e', fn: toggleArchive },
      // { key: 'k', fn: goNewer },
      // { key: 'j', fn: goOlder },
      // { key: '[', fn: goNewerAndArchive },
      // { key: ']', fn: goOlderAndarchive },
    ])

    return {
      format,
      toggleRead,
      toggleArchive,
      // goNewer,
      // goOlder,
      // goNewerAndArchive,
      // goOlderAndarchive,
    }
  },
  props: {
    email: {
      type: [Object, String],
      required: true,
    },
  },
}
</script>
