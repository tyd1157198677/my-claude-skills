# Vue 3 组件示例

## 基础组件示例

### Counter.vue
```vue
<script setup lang="ts">
import { ref, computed } from 'vue'

interface Props {
  initialValue?: number
  step?: number
}

const props = withDefaults(defineProps<Props>(), {
  initialValue: 0,
  step: 1
})

const emit = defineEmits<{
  change: [value: number]
}>()

const count = ref(props.initialValue)

const doubleCount = computed(() => count.value * 2)

function increment() {
  count.value += props.step
  emit('change', count.value)
}

function decrement() {
  count.value -= props.step
  emit('change', count.value)
}
</script>

<template>
  <div class="counter">
    <p class="count">计数：{{ count }}</p>
    <p class="double">双倍：{{ doubleCount }}</p>
    <div class="buttons">
      <button @click="decrement">-{{ step }}</button>
      <button @click="increment">+{{ step }}</button>
    </div>
  </div>
</template>

<style scoped>
.counter {
  padding: 1.5rem;
  border: 1px solid #e0e0e0;
  border-radius: 8px;
  max-width: 300px;
}

.count {
  font-size: 1.5rem;
  color: #333;
}

.double {
  font-size: 1.25rem;
  color: #666;
  margin: 0.5rem 0;
}

.buttons {
  display: flex;
  gap: 0.5rem;
  margin-top: 1rem;
}

button {
  padding: 0.5rem 1rem;
  border: none;
  border-radius: 4px;
  background: #42b883;
  color: white;
  cursor: pointer;
  transition: background 0.2s;
}

button:hover {
  background: #369970;
}
</style>
```

## 组合式函数示例

### useCounter.ts
```typescript
import { ref, computed } from 'vue'

export interface UseCounterOptions {
  initialValue?: number
  step?: number
  max?: number
  min?: number
}

export function useCounter(options: UseCounterOptions = {}) {
  const {
    initialValue = 0,
    step = 1,
    max = Infinity,
    min = -Infinity
  } = options

  const count = ref(initialValue)

  const canIncrement = computed(() => count.value + step <= max)
  const canDecrement = computed(() => count.value - step >= min)

  function increment() {
    if (canIncrement.value) {
      count.value += step
    }
  }

  function decrement() {
    if (canDecrement.value) {
      count.value -= step
    }
  }

  function reset() {
    count.value = initialValue
  }

  function set(value: number) {
    count.value = Math.max(min, Math.min(max, value))
  }

  return {
    count,
    canIncrement,
    canDecrement,
    increment,
    decrement,
    reset,
    set
  }
}
```

### useFetch.ts
```typescript
import { ref, computed, watch } from 'vue'

export interface UseFetchOptions<T> {
  immediate?: boolean
  errorHandler?: (error: Error) => void
}

export function useFetch<T>(
  url: string,
  options: UseFetchOptions<T> = {}
) {
  const { immediate = true, errorHandler } = options

  const data = ref<T | null>(null)
  const error = ref<Error | null>(null)
  const isLoading = ref(false)
  const isLoaded = computed(() => !isLoading.value && data.value !== null)

  async function execute() {
    isLoading.value = true
    error.value = null

    try {
      const response = await fetch(url)
      if (!response.ok) {
        throw new Error(`HTTP ${response.status}: ${response.statusText}`)
      }
      data.value = await response.json()
    } catch (err) {
      error.value = err instanceof Error ? err : new Error(String(err))
      errorHandler?.(error.value)
    } finally {
      isLoading.value = false
    }
  }

  if (immediate) {
    execute()
  }

  return {
    data,
    error,
    isLoading,
    isLoaded,
    execute,
    refresh: execute
  }
}
```

## Pinia Store 示例

### counterStore.ts
```typescript
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'

export const useCounterStore = defineStore('counter', () => {
  // 状态
  const count = ref(0)
  const history = ref<number[]>([])

  // 计算属性
  const doubleCount = computed(() => count.value * 2)
  const lastOperation = computed(() =>
    history.length > 0 ? history[history.length - 1] : null
  )

  // 方法
  function increment(amount = 1) {
    history.value.push(count.value)
    count.value += amount
  }

  function decrement(amount = 1) {
    history.value.push(count.value)
    count.value -= amount
  }

  function reset() {
    history.value.push(count.value)
    count.value = 0
  }

  function undo() {
    if (history.value.length > 0) {
      count.value = history.value.pop()!
    }
  }

  return {
    count,
    doubleCount,
    history,
    lastOperation,
    increment,
    decrement,
    reset,
    undo
  }
}, {
  persist: true // 需要 pinia-plugin-persistedstate
})
```

## 路由配置示例

### router/index.ts
```typescript
import { createRouter, createWebHistory } from 'vue-router'
import type { RouteRecordRaw } from 'vue-router'

const routes: RouteRecordRaw[] = [
  {
    path: '/',
    name: 'home',
    component: () => import('@/views/HomeView.vue'),
    meta: { title: '首页' }
  },
  {
    path: '/about',
    name: 'about',
    component: () => import('@/views/AboutView.vue'),
    meta: { title: '关于' }
  },
  {
    path: '/users/:id',
    name: 'user-detail',
    component: () => import('@/views/UserDetail.vue'),
    props: true,
    meta: {
      title: '用户详情',
      requiresAuth: true
    }
  },
  {
    path: '/:pathMatch(.*)*',
    name: 'not-found',
    component: () => import('@/views/NotFound.vue'),
    meta: { title: '404' }
  }
]

const router = createRouter({
  history: createWebHistory(import.meta.env.BASE_URL),
  routes
})

// 路由守卫
router.beforeEach((to, from, next) => {
  document.title = to.meta.title as string || 'Vue App'

  const requiresAuth = to.meta.requiresAuth
  const isAuthenticated = localStorage.getItem('token')

  if (requiresAuth && !isAuthenticated) {
    next({ name: 'home' })
  } else {
    next()
  }
})

export default router
```
