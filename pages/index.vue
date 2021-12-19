<script>
import { mapMutations } from 'vuex'

export default {
  name: 'IndexPage',
  data: () => ({
    tab: null,
    items: [
      { tab: 'pending', content: '' },
      { tab: 'completed', content: '' },
    ],
    inputTodo: '',
    inputTodoRules: [(v) => !!v || 'This field is required'],
  }),
  computed: {
    todos() {
      return this.$store.state.list
    },
  },
  methods: {
    filterByStatus(obj, isDone) {
      if (obj) {
        if (!isDone) return obj.filter((v) => !v.done)

        return obj.filter((v) => v.done)
      }
    },
    addTodo(e) {
      if (this.$refs.form.validate()) {
        this.$store.commit('add', this.inputTodo)
        this.inputTodo = ''
      }
    },
    ...mapMutations({
      toggle: 'toggle',
    }),
  },
}
</script>

<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="12" md="4">
      <v-card>
        <v-card-title class="headline"> Checklist </v-card-title>

        <v-card-text>
          <v-tabs v-model="tab">
            <v-tab v-for="item in items" :key="item.tab">
              {{ item.tab }}
            </v-tab>
          </v-tabs>

          <v-tabs-items v-model="tab">
            <v-tab-item v-for="item in items" :key="item.tab">
              <v-card flat>
                <v-card-text>
                  <v-list-item-group v-if="item.tab == 'pending'">
                    <v-list-item
                      v-for="todo in filterByStatus(todos, false)"
                      :key="todo.text"
                      @click="toggle(todo)"
                    >
                      <template #default="{ active }">
                        <v-list-item-action>
                          <v-checkbox :input-value="active"></v-checkbox>
                        </v-list-item-action>
                        <v-list-item-content>
                          <v-list-item-subtitle>{{
                            todo.text
                          }}</v-list-item-subtitle>
                        </v-list-item-content>
                      </template>
                    </v-list-item>
                  </v-list-item-group>

                  <v-list-item-group v-else>
                    <v-list-item
                      v-for="todo in filterByStatus(todos, true)"
                      :key="todo.text"
                      @click="toggle(todo)"
                    >
                      <template #default="{ active }">
                        <v-list-item-action>
                          <v-checkbox :input-value="active"></v-checkbox>
                        </v-list-item-action>
                        <v-list-item-content>
                          <v-list-item-subtitle>{{
                            todo.text
                          }}</v-list-item-subtitle>
                        </v-list-item-content>
                      </template>
                    </v-list-item>
                  </v-list-item-group>
                </v-card-text>
              </v-card>
            </v-tab-item>
          </v-tabs-items>

          <div>
            <v-form ref="form" v-model="valid" @submit.prevent="addTodo">
              <v-text-field
                v-model="inputTodo"
                :rules="inputTodoRules"
                label="Add an item here"
                required
              ></v-text-field>
              <v-btn
                :disabled="!valid"
                color="primary"
                class="mr-4"
                @click="addTodo"
              >
                Add item
              </v-btn>
            </v-form>
          </div>
        </v-card-text>
      </v-card>
    </v-col>
  </v-row>
</template>
