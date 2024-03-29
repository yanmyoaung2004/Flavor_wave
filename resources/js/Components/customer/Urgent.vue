<script setup>
import { ref } from "vue";
import ConfrimModal from "@/Components/Modals/ConfrimModal.vue";
import CancelModal from "@/Components/Modals/CancelModal.vue";
import TableLayout from "@/Layouts/TableLayout.vue";
import UrgentOrderModal from "../Modals/UrgentOrderModal.vue";
import { formatPrice } from "./helper";

defineProps({
    urgents: {
        type: Array,
    },
    user: {
        type: Object,
    },
});

const headers = ref([
    "PreOrder ID",
    "Product Names",
    "Quantity",
    "Total Price",
    "Preorder Date",
    "Status",
    "Details",
]);
const confrimation = ref(false);
const cancelconfrimation = ref(false);
const preorderID = ref(null);
const urgentObj = ref(null);
const urgentConfrimation = ref(false);

const showModal = (id) => {
    confrimation.value = true;
    preorderID.value = id;
};

const showCancelModal = (id) => {
    cancelconfrimation.value = true;
    preorderID.value = id;
};

const showUrgentOrderModal = (urgent) => {
    urgentConfrimation.value = true;
    urgentObj.value = urgent;
};
</script>

<template>
    <TableLayout
        :headers="headers"
        :is-admin="$page.props.auth.user.isAdmin"
        :is-department="$page.props.auth.user.department?.name === 'SALE'"
    >
        <template #tbody>
            <tr
                class="border-b item"
                v-for="(urgent, index) in urgents"
                :key="urgent.id"
            >
                <td class="py-4 px-2">{{ index + 1 }}</td>
                <td class="py-4 px-2 text-center">{{ urgent.id }}</td>
                <td class="py-3 text-center" style="width: 400px">
                    <span v-for="product in urgent.products" :key="product.id">
                        {{
                            product.name.length > 15
                                ? product.name.slice(0, 10) + "..."
                                : product.name
                        }},
                    </span>
                </td>

                <td class="py-4 px-2 text-center">
                    {{ urgent.order_quantity }}
                </td>
                <td class="py-4 px-2 text-center">
                    {{ formatPrice(urgent.total_price) }}$
                </td>
                <td class="py-4 px-2 text-center">
                    {{ new Date(urgent.created_at).toLocaleDateString() }}
                </td>
                <td
                    class="py-4 px-2 text-center"
                    :class="{
                        'text-yellow-400': urgent.status === 'pending',
                        'text-blue-400': urgent.status === 'order',
                        'text-green-500': urgent.status === 'deliver',
                        'text-red-500': urgent.status === 'cancel',
                    }"
                >
                    {{ urgent.status }}
                </td>
                <td class="py-4 px-2 text-center">
                    <button
                        class="text-blue-500 hover:text-blue-600 hover:underline duration-200 font-semibold"
                        @click="showUrgentOrderModal(urgent)"
                    >
                        See Details
                    </button>
                </td>

                <td
                    class="py-4 px-2 text-center"
                    v-show="user.isAdmin && user.department?.name === 'SALE'"
                >
                    <button
                        class="text-blue-500 hover:text-blue-600 hover:underline duration-200 font-semibold"
                        @click="showModal(urgent.id)"
                    >
                        confrim
                    </button>
                    <button
                        class="text-red-500 hover:text-red-600 hover:underline duration-200 font-semibold ms-2"
                        @click="showCancelModal(urgent.id)"
                    >
                        cancel
                    </button>
                </td>
            </tr>
        </template>
    </TableLayout>
    <ConfrimModal
        :preorder_id="preorderID"
        :confrimation="confrimation"
        @hide-modal="confrimation = false"
    />
    <CancelModal
        :preorder_id="preorderID"
        :cancelconfrimation="cancelconfrimation"
        @cancel-modal="cancelconfrimation = false"
    />
    <UrgentOrderModal
        :urgent="urgentObj"
        :urgentConfrimation="urgentConfrimation"
        @hide-modal="urgentConfrimation = false"
    />
</template>
