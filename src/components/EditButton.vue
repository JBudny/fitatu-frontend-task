<template>
    <div class="editButton">
        <button
            class="editButton__button"
            v-if="!edit"
            v-on:click="editEmployee">
            Edit
        </button>
        <template v-else>
            <button
                class="editButton__button"
                v-bind:class="{ disabled: !validated }"
                v-on:click="saveChanges"
                :disabled="validated == false">
                Save
            </button>
            <button
                class="editButton__button"
                v-on:click="cancelChanges">
                Cancel
            </button>
        </template>
    </div>
</template>

<script>
export default {
    props: ['edit', 'validated'],
    methods: {
        editEmployee () {
            this.$emit('editEmployee');
        },
        saveChanges () {
            this.$emit('saveChanges');
        },
        cancelChanges () {
            this.$emit('cancelChanges');
        }
    }
}
</script>

<style lang="scss" scoped>
    .editButton {
        display: flex;
        flex-direction: column;
        align-items: center;

        &__button {
            width:100%;
            margin: 0.125rem;
            padding: 0.125rem;
            border: none;
            border-radius: 3px;
            background: #4FA9F6;
            color: #ffffff;
            cursor: pointer;
            transition: 250ms ease-in-out;
            -webkit-appearance: none;
            -moz-appearance: none;

            &:hover{
            background: #88C1F7;
            }
        }

        .disabled {
            background: #a3a3a3;
            cursor: context-menu;

            &:hover{
                background: #a3a3a3;
            }

            &:focus {
                outline: none 
            }
        }
    }

    @media screen and (max-width: 768px) {
        .editButton {
            &__button {
                font-size: 1.1rem;
                font-weight: bold;
                border-radius: 5px;
            }
        }
    }    
</style>