<script setup>
import TableLayout from "@/Layouts/TableLayout.vue";
import Body from "@/Components/Table/Body.vue";
import Button from "@/Components/Button.vue";
import { router } from "@inertiajs/vue3";
import { showEdit, confrim, errors } from "../../../composable/editDeliver";
import { ref } from "vue";

defineProps({
    logistics: {
        type: Array,
    },
});

const headers = ref([
    "Preorder ID",
    "Driver ID",
    "Quantity",
    "Deliver Date",
    "Status",
]);

const deleteLogistic = (id) => {
    router.delete(route("logistic.destroy", id));
};

const edit = (e, logistic, index) => {
    showEdit(e, logistic, index);
};

const confrimData = (index, logistic) => {
    confrim(index, logistic);
};
</script>

<template>
    <TableLayout
        :headers="headers"
        :is-admin="$page.props.auth.user.isAdmin"
        :is-department="$page.props.auth.user.department.name === 'LOGISTIC'"
    >
        <template #tbody>
            <tr
                class="border-b item"
                v-for="(logistic, index) in logistics"
                :key="logistic.id"
            >
                <td class="py-4">{{ index + 1 }}</td>
                <Body
                    :text-id="`preorderID${index}`"
                    :error-id="`errorp${index}`"
                    :value="logistic.preorder_id"
                    :error="errors.preorder_id"
                />
                <Body
                    :text-id="`driverID${index}`"
                    :error-id="`errord${index}`"
                    :value="logistic.driver_id"
                    :error="errors.driver_id"
                />
                <Body
                    :text-id="`quantity${index}`"
                    :error-id="`errorq${index}`"
                    :value="logistic.quantity"
                    :error="errors.quantity"
                />
                <td class="py-4 text-center">
                    {{ new Date(logistic.created_at).toLocaleDateString() }}
                </td>
                <td class="py-4 text-center text-green-500">
                    {{ logistic.status }}
                </td>
                <td class="py-4 space-x-3 text-center">
                    <Button
                        type="button"
                        text="Edit"
                        :id="`editBtn${index}`"
                        class="text-blue-500 hover:text-blue-600 duration-150 font-bold hover:underline"
                        @click="edit($event, logistic, index)"
                    />
                    <Button
                        type="button"
                        text="Confrim"
                        :id="`confirmBtn${index}`"
                        class="hidden text-blue-500 hover:text-blue-600 duration-150 font-bold hover:underline"
                        @click="confrimData(index, logistic)"
                    />
                    <Button
                        type="button"
                        text="Delete"
                        class="text-red-500 hover:text-red-600 duration-150 font-bold hover:underline"
                        @click="deleteLogistic(logistic.id)"
                    />
                </td>
            </tr>
        </template>
    </TableLayout>
</template>
