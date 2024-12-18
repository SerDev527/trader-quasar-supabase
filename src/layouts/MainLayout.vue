<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated class="bg-dark text-white">
      <q-toolbar>
        <q-toolbar-title class="cursor-pointer" @click="goHome">
          Traddio
        </q-toolbar-title>

        <div class="row justify-center col-grow">
          <div class="col-12 col-sm-8 col-md-6">
            <q-input
              v-model="searchQuery"
              dense
              outlined
              dark
              placeholder="Search anything i.e APPLE or BTC"
              class="search-input"
              @input="handleSearch"
            >
              <template v-slot:prepend>
                <q-icon name="search" />
              </template>
            </q-input>
          </div>
        </div>

        <q-btn-dropdown flat color="white" icon="person">
          <q-list>
            <q-item
              clickable
              v-close-popup
              @click="handlerLogout"
              class="text-black"
            >
              <q-item-section>
                <q-item-label>Logout</q-item-label>
              </q-item-section>
            </q-item>
          </q-list>
        </q-btn-dropdown>
      </q-toolbar>
    </q-header>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent, ref, provide } from "vue";
import { useRouter } from "vue-router";

import userAuthUser from "../composables/UserAuthUser";
import useNotify from "src/composables/UseNotify";
import useDialog from "src/composables/UseDialog";

export default defineComponent({
  name: "MainLayout",

  setup() {
    const router = useRouter();
    const { logout } = userAuthUser();
    const { notifyError, notifySuccess } = useNotify();
    const { dialogShow } = useDialog();
    const searchQuery = ref("");

    const handleSearch = (value) => {
      searchQuery.value = value;
    };

    // Provide the search query to child components
    provide("searchQuery", searchQuery);

    const handlerLogout = async () => {
      dialogShow({
        tittle: "To go out",
        message: "Are you sure you want to exit the application?",
        class: "text-white",
        dark: true,
        persistent: true,
        ok: {
          push: true,
          color: "primary",
          label: "Yes",
        },
        cancel: {
          push: true,
          color: "negative",
          label: "No",
        },
      })
        .onOk(async () => {
          try {
            await logout();
            notifySuccess("Bye bye!");
            router.replace({
              name: "login",
            });
          } catch (error) {
            notifyError(error.message);
          }
        })
        .onCancel(async () => {
          notifySuccess("Oops, I was going to but I came back!");
        });
    };

    return {
      handlerLogout,
      searchQuery,
      handleSearch,
    };
  },
});
</script>

<style lang="scss">
.col-grow {
  flex-grow: 1;
  padding: 0 20px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.search-input {
  width: 100%;
  max-width: 600px;

  .q-field__control {
    background: rgba(255, 255, 255, 0.05);
    border-radius: 20px;

    &:hover {
      background: rgba(255, 255, 255, 0.08);
    }
  }

  .q-field__marginal {
    color: rgba(255, 255, 255, 0.7);
  }

  input {
    color: white !important;
    &::placeholder {
      color: rgba(255, 255, 255, 0.5);
    }
  }
}

.q-toolbar {
  padding: 0 20px;
  height: 64px;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.q-toolbar__title {
  flex: 0 0 auto;
}

@media (max-width: 599px) {
  .search-input {
    width: 100%;
  }

  .q-toolbar {
    padding: 0 10px;
  }

  .col-grow {
    padding: 0 10px;
  }
}

// Add styles for dialog
:deep(.q-dialog__title),
:deep(.q-dialog__message) {
  color: white !important;
}

.q-dialog {
  .q-card {
    background: #1e1e1e;
  }
}
</style>
