<template>
	<div class="flex h-screen w-screen flex-col bg-surface-base">
		<Navbar :primaryButton="{ hide: true }">
			<template #actions>
				<Button variant="ghost" @click="toggleTheme">
					<template #icon>
						<component :is="isDark ? Sun : Moon" class="size-4 stroke-[1.5]" />
					</template>
				</Button>
			</template>
		</Navbar>

		<div class="flex flex-1 flex-col items-center justify-center gap-10 px-6">
			<div class="flex flex-col gap-2 text-center">
				<h1 class="text-5xl font-semibold text-ink-gray-9">Start a new presentation</h1>
				<p class="text-base text-ink-gray-5">How would you like to build it?</p>
			</div>

			<div class="grid w-full max-w-2xl grid-cols-1 gap-6 md:grid-cols-2">
				<div
					v-for="option in options"
					:key="option.key"
					class="flex flex-col gap-8 rounded-2xl border border-outline-gray-2 bg-surface-cards p-5 transition-colors hover:border-outline-gray-3"
				>
					<div class="flex items-start justify-between">
						<div
							class="flex size-12 items-center justify-center rounded-xl bg-surface-gray-2 text-ink-gray-7"
						>
							<component :is="option.icon" class="size-5 stroke-[1.5]" />
						</div>
					</div>

					<div class="flex flex-col gap-2">
						<h2 class="text-xl font-semibold text-ink-gray-9">{{ option.title }}</h2>
						<ul class="list-disc space-y-2 pl-5 text-base text-ink-gray-7">
							<li v-for="(step, index) in option.steps" :key="index">{{ step }}</li>
						</ul>
					</div>

					<div class="mt-auto">
						<Button :variant="option.button.variant" size="md" @click="option.button.onClick">
							{{ option.button.label }}
						</Button>
					</div>
				</div>
			</div>
		</div>
	</div>
</template>

<script setup>
import { computed, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import { useRootStore } from '@/stores/root'

import { Badge, Button } from 'frappe-ui'

import { Sparkle, LayoutGrid, Sun, Moon } from 'lucide-vue-next'

import Navbar from '@/apps/slides/components/Navbar.vue'

const router = useRouter()
const rootStore = useRootStore()

onMounted(() => {
	const current = document.documentElement.getAttribute('data-theme')
	if (current === 'dark' || current === 'light') rootStore.setTheme(current)
})

const isDark = computed(() => rootStore.theme === 'dark')

const toggleTheme = () => {
	rootStore.setTheme(isDark.value ? 'light' : 'dark')
}

const openBlankPresentation = () => {
	router.push({ name: 'slides-editor-new' })
}

const startWithAI = () => {
	// TODO: navigate to context builder route once it exists
}

const options = [
	{
		key: 'ai',
		icon: Sparkle,
		title: 'AI Assisted',
		steps: [
			'Add files & links, describe the goal',
			'Answer a few intent questions',
			'Shape the story, then generate',
		],
		recommended: true,
		button: { label: 'Start with AI', variant: 'solid', onClick: startWithAI },
	},
	{
		key: 'manual',
		icon: LayoutGrid,
		title: 'Manual',
		steps: ['Blank canvas or a template', 'Add and arrange slides', 'Full manual control'],
		recommended: false,
		button: { label: 'Start from scratch', variant: 'subtle', onClick: openBlankPresentation },
	},
]
</script>
