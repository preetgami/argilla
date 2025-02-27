<!--
  - coding=utf-8
  - Copyright 2021-present, the Recognai S.L. team.
  -
  - Licensed under the Apache License, Version 2.0 (the "License");
  - you may not use this file except in compliance with the License.
  - You may obtain a copy of the License at
  -
  -     http://www.apache.org/licenses/LICENSE-2.0
  -
  - Unless required by applicable law or agreed to in writing, software
  - distributed under the License is distributed on an "AS IS" BASIS,
  - WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  - See the License for the specific language governing permissions and
  - limitations under the License.
  -->

<template>
  <div v-if="textSpans.length" ref="list" class="content__input">
    <text-span-static
      v-for="(token, i) in textSpans"
      :key="i"
      :record="record"
      :token="token"
      :datasetName="datasetName"
      :datasetEntities="datasetEntities"
      :datasetQuery="datasetQuery"
    />
  </div>
</template>

<script>
export default {
  props: {
    entities: {
      type: Array,
      required: true,
    },
    datasetName: {
      type: String,
      required: true,
    },
    datasetEntities: {
      type: Array,
      required: true,
    },
    datasetQuery: {
      type: Object,
      required: true,
    },
    record: {
      type: Object,
      required: true,
    },
    visualTokens: {
      type: Array,
      required: true,
    },
    origin: {
      type: String,
      required: true,
    },
  },
  computed: {
    textSpans() {
      // TODO Simplify !!!
      const normalizedEntities = (entities, tokens) => {
        const tokenForChar = (character, tokens) => {
          const tokenIdx = tokens.findIndex(
            (token) => token.start <= character && character < token.end
          );
          return tokenIdx >= 0 ? tokenIdx : undefined;
        };
        return entities.map((entity) => {
          const start_token = tokenForChar(entity.start, tokens);
          const end_token = tokenForChar(entity.end - 1, tokens);
          return entity.start_token && entity.end_token
            ? entity
            : { ...entity, start_token, end_token: end_token + 1 };
        });
      };

      let idx = 0;
      let textSpans = [];
      const entities = normalizedEntities(this.entities, this.visualTokens);
      while (idx < this.visualTokens.length) {
        let index = textSpans.length;
        const entityArray = entities.filter(
          (entity) => entity.start_token <= idx && idx < entity.end_token
        );
        const entity = entityArray.find((e) =>
          index > 0 ? e.start >= textSpans[index - 1].end : true
        );
        if (entity) {
          textSpans.push({
            entity,
            tokens: this.visualTokens.slice(
              entity.start_token,
              entity.end_token
            ),
            start: entity.start,
            end: entity.end,
            origin: this.origin,
            charsBetweenTokens: entity.charsBetweenTokens,
          });
          idx = entity.end_token;
        } else {
          const token = this.visualTokens[idx];
          textSpans.push({
            entity: undefined,
            tokens: [token],
            start: token.start,
            end: token.end,
            origin: this.origin,
            charsBetweenTokens: token.charsBetweenTokens,
          });
          idx++;
        }
      }
      return textSpans;
    },
  },
};
</script>

<style lang="scss" scoped>
.content {
  &__input {
    ::selection {
      background: none !important;
    }
  }
}
</style>
