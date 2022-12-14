<!--
Copyright 2022 Google Inc. All rights reserved.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
-->
<template>
  <div>
    <v-row no-gutters class="pa-3" :class="$vuetify.theme.dark ? 'dark-hover' : 'light-hover'">
      <div @click="expanded = !expanded" style="cursor: pointer; font-size: 0.9em">
        <v-icon v-if="!expanded">mdi-chevron-right</v-icon>
        <v-icon v-else>mdi-chevron-down</v-icon>
        {{ sigmaRule.title }}
      </div>
      <v-spacer></v-spacer>
      <div><v-icon>mdi-dots-vertical</v-icon></div>
    </v-row>

    <v-expand-transition>
      <div v-show="expanded" class="pa-4 pt-0">
        <div style="font-size: 0.9em" class="mt-2">
          <v-simple-table dense>
            <template v-slot:default>
              <tbody>
                <tr v-for="(v, k) in sigmaRuleSummary" :key="v.id">
                  <td>
                    <strong>{{ k | capitalize }}</strong>
                  </td>
                  <td>
                    <span v-if="k === 'references'">
                      <div v-for="ref in v" :key="ref">
                        <a :href="ref" target="new">{{ ref }}</a>
                      </div>
                    </span>
                    <span v-else-if="k === 'falsepositives'">
                      <v-chip v-for="tag in v" :key="tag" rounded x-small class="mr-2">{{ tag }}</v-chip>
                    </span>
                    <span v-else-if="k === 'tags'">
                      <v-chip v-for="tag in v" :key="tag" rounded x-small class="mr-2">{{ tag }}</v-chip>
                    </span>
                    <span v-else>
                      {{ v }}
                    </span>
                  </td>
                </tr>
              </tbody>
            </template>
          </v-simple-table>
        </div>

        <div class="mt-3">
          <v-btn @click="search(sigmaRule.search_query)" small depressed color="primary">Search</v-btn>
        </div>
      </div>
    </v-expand-transition>
    <v-divider></v-divider>
  </div>
</template>

<script>
import EventBus from '../../main'

const defaultQueryFilter = () => {
  return {
    from: 0,
    terminate_after: 40,
    size: 40,
    indices: '_all',
    order: 'asc',
    chips: [],
  }
}

export default {
  props: ['sigmaRule'],
  data: function () {
    return {
      expanded: false,
    }
  },
  computed: {
    sketch() {
      return this.$store.state.sketch
    },
    sigmaRuleSummary() {
      const fields = ['author', 'description', 'references', 'date', 'modified', 'falsepositives', 'level', 'tags']
      return Object.fromEntries(Object.entries(this.sigmaRule).filter(([key]) => fields.includes(key)))
    },
  },
  methods: {
    search(queryString) {
      let eventData = {}
      eventData.doSearch = true
      eventData.queryString = queryString
      eventData.queryFilter = defaultQueryFilter()
      console.log(eventData)
      EventBus.$emit('setQueryAndFilter', eventData)
    },
  },
}
</script>

<style scoped lang="scss"></style>
