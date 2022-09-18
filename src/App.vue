<template>
  <div>
    Test Rxjs Ajax
    <ul class="text-red-600">
      <li v-for="item in frameworks" :key="item">{{ item.name }}</li>
    </ul>
  </div>
</template>

<script lang="ts">
import { defineComponent, onMounted, ref, type Ref } from "vue"
import { ajax } from 'rxjs/ajax'
import { map, catchError, of } from 'rxjs'

export default defineComponent({
    setup() {
        let frameworks: Ref<any[]> = ref([])
        const urlPath = `https://api.github.com/search/topics?q=vue`
        const users: Observable<any> = ajax({
            url: urlPath,
            method: 'GET',
        }).pipe(
            map(resp => {
                console.log('users: ', resp)
                const { response } = resp
                return response
            }),
            catchError(error => {
                console.log('error: ', error)
                return of(error)
            })
        )

        onMounted(() => {
            users.subscribe({
                next: value => {
                    console.log(value)
                    frameworks.value = value.items
                },
                error: err => console.log(err)
            })
        })
        return {
            frameworks,
        }
    },
})
</script>
