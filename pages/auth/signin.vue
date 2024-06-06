<script lang="ts" setup>
import type { FormSubmitEvent } from '@nuxt/ui/dist/runtime/types';
import SigninSchema from '~/schemas/Signin.schema';
import type { z } from 'zod';

const formState = reactive({
    email: undefined,
    password: undefined,
});

const isLoading = ref(false);

const { signIn } = useAuth();

async function handleSignin(
    event: FormSubmitEvent<z.output<typeof SigninSchema>>
) {
    try {
        isLoading.value = true;
        const response = await signIn('credentials', {
            email: event.data.email,
            password: event.data.password,
            redirect: false,
        });

        // @ts-expect-error
        if (!response.error) {
            useRouter().push('/');
        }
    } catch (e) {
    } finally {
        isLoading.value = false;
    }
}
</script>

<template>
    <WrapperAuth title="Login to your account">
        <UForm :state="formState" :schema="SigninSchema" @submit="handleSignin">
            <UFormGroup class="mb-4" name="email" label="Email">
                <UInput v-model="formState.email" type="email" />
            </UFormGroup>
            <UFormGroup class="mb-4" name="password" label="Password">
                <UInput v-model="formState.password" type="password" />
            </UFormGroup>

            <UButton type="submit" block :loading="isLoading">Signin</UButton>
        </UForm>
    </WrapperAuth>
</template>

<style>
.right {
    background: linear-gradient(-45deg, #22c55e, #10b981, #84cc16, #23d5ab);
    background-size: 400% 400%;
    animation: gradient 15s ease infinite;
}

@keyframes gradient {
    0% {
        background-position: 0% 50%;
    }
    50% {
        background-position: 100% 50%;
    }
    100% {
        background-position: 0% 50%;
    }
}
</style>
