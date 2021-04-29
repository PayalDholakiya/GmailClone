<template>
  <table class="mail-table">
    <tbody>
      <tr
        v-for="email in unarchivedEmails"
        :key="email.id"
        :class="['clickable', email.read ? 'read' : '']"
        @click="openEmail(email)"
      >
        <td>
          <input type="checkbox" />
        </td>
        <td>{{ email.from }}</td>
        <td>
          <p>
            <strong>{{ email.subject }}</strong> - {{ email.body }}
          </p>
        </td>
        <td>
          <!-- {{ email.sentAt }} -->
          {{ format(new Date(email.sentAt), 'MMM do yyyy') }}
        </td>
        <td><button @click="archiveEmail(email)">Archive</button></td>
      </tr>
    </tbody>
  </table>
  <!-- <ModalView v-if="open" @close="close()">
    <MailView :email="openedEmail" />
  </ModalView> -->
  <ModalView v-if="openedEmail" @closeModal="close()">
    <MailView :email="openedEmail" />
  </ModalView>
</template>

<script>
import { format } from 'date-fns'
import axios from 'axios'
import { ref, computed } from 'vue'
import MailView from '@/components/MailView.vue'
import ModalView from '@/components/ModalView.vue'

export default {
  name: 'Mail',
  async setup() {
    // const sortedEmails = computed(() => {
    //   return
    // })

    let { data: emails } = await axios.get('http://localhost:3000/emails')
    function openEmail(email) {
      this.open = true
      email.read = true
      this.updateEmail(email)
      this.openedEmail = email
      console.log(email, 'j')
      console.log(this.openedEmail.subject, ' this.openedEmail')
    }
    function updateEmail(email) {
      axios.put(`http://localhost:3000/emails/${email.id}`, email)
    }
    function archiveEmail(email) {
      email.archived = true
      this.updateEmail(email)
    }
    function close() {
      window.location.reload()
    }
    function changeEmail({
      toggleRead,
      toggleArchive,
      save,
      closeModal,
      changeIndex,
    }) {
      let email = this.openEmail
      if (toggleRead) {
        console.log('toggleRead')
        email.read = !email.read
      }
      if (toggleArchive) {
        console.log('toggleArchive')
        email.archived = !email.archived
      }
      if (save) {
        console.log('save')
        this.updateEmail(email)
      }
      if (closeModal) {
        console.log('closeModal')
        this.openedEmail = null
      }
      if (changeIndex) {
        console.log('changeIndex')
        let emails = this.unarchivedEmails
        let currentIndex = emails.indexOf(this.openedEmail)
        let newEmail = emails[currentIndex + changeIndex]
        this.openEmail(newEmail)
      }
    }
    return {
      format,
      emails: ref(emails),
      openedEmail: '',
      open: false,
      openEmail,
      updateEmail,
      archiveEmail,
      close,
      changeEmail,
    }
  },
  components: {
    MailView,
    ModalView,
  },
  computed: {
    sortedEmails() {
      return this.emails.sort((e1, e2) => {
        return e1.sentAt < e2.sentAt ? 1 : -1
      })
    },
    unarchivedEmails() {
      return this.sortedEmails.filter((e) => !e.archived)
    },
  },
}
</script>

<style scoped>
.mail-table {
  max-width: 1000px;
  margin: auto;
  border-collapse: collapse;
}
.mail-table tr.read {
  background-color: #eee;
}
.mail-table tr {
  height: 40px;
}
.mail-table td {
  border-bottom: 1px solid black;
  padding: 5px;
  text-align: left;
}
.mail-table tr:first-of-type td {
  border-top: 1px solid black;
}
.mail-table td p {
  max-height: 1.2em;
  overflow-y: hidden;
  margin: 0;
}
</style>
