<script lang="ts" setup>
import type { z } from 'zod';
import SignupSchema from '~/schemas/Signup.schema';
import type { FormSubmitEvent } from '@nuxt/ui/dist/runtime/types';

const isLoading = ref(false);

const formState = reactive({
    name: undefined,
    email: undefined,
    password: undefined,
    passwordConfirm: undefined,
});

async function handleSubmit(
    event: FormSubmitEvent<z.output<typeof SignupSchema>>
) {
    try {
        isLoading.value = true;

        useFetch('/api/auth/signup', {
            method: 'POST',
            body: event.data,
            watch: false,
        });

        useToast().add({
            title: 'Account created!',
            description:
                'Your account has been created successfully, Redirecting you to the sign in page',
            timeout: 2000,
        });

        useRouter().push({
            name: 'auth-signin',
        });
    } catch (e: any) {
        useToast().add({
            title: 'Error',
            description: e.message || 'Something went wrong!',
            timeout: 2000,
        });
    } finally {
        isLoading.value = false;
    }
}
</script>
<template>
    <WrapperAuth title="Create a free account">
        <UForm :state="formState" :schema="SignupSchema" @submit="handleSubmit">
            <UFormGroup class="mb-4" name="name" label="Name">
                <UInput v-model="formState.name"></UInput>
            </UFormGroup>
            <UFormGroup class="mb-4" name="email" label="Email">
                <UInput type="email" v-model="formState.email"></UInput>
            </UFormGroup>
            <UFormGroup class="mb-4" name="password" label="Password">
                <UInput type="password" v-model="formState.password"></UInput>
            </UFormGroup>
            <UFormGroup
                class="mb-4"
                name="passwordConfirm"
                label="Confirm password"
            >
                <UInput
                    type="password"
                    v-model="formState.passwordConfirm"
                ></UInput>
            </UFormGroup>

            <UButton block type="submit" :loading="isLoading">Submit</UButton>
        </UForm>
    </WrapperAuth>
</template>
