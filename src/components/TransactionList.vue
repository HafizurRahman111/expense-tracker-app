<template>
    <div class="container mt-4">
        <h2 class="mb-3 text-center">Transaction List</h2>
        <p v-if="transactions.length < 6" class="text-muted text-center">
            Pagination is enabled when there are more than 5 transactions.
        </p>
        <div class="d-flex justify-content-between align-items-center mb-3">
            <div class="d-flex gap-2">
                <button class="btn" :class="filterType === 'all' ? 'btn-primary' : 'btn-outline-primary'"
                    @click="filterType = 'all'; currentPage = 1">
                    All
                </button>
                <button class="btn" :class="filterType === 'income' ? 'btn-success' : 'btn-outline-success'"
                    @click="filterType = 'income'; currentPage = 1">
                    Income
                </button>
                <button class="btn" :class="filterType === 'expense' ? 'btn-danger' : 'btn-outline-danger'"
                    @click="filterType = 'expense'; currentPage = 1">
                    Expense
                </button>
            </div>
        </div>
        <div class="table-responsive">
            <table class="table table-bordered table-hover shadow-sm">
                <thead class="table-dark">
                    <tr>
                        <th>#</th>
                        <th>Title</th>
                        <th>Amount</th>
                        <th>Type</th>
                        <th>Date</th>
                        <th>Actions</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(transaction, index) in paginatedTransactions" :key="index">
                        <td>{{ (currentPage - 1) * perPage + index + 1 }}</td>
                        <td>{{ transaction.title }}</td>
                        <td :class="transaction.type === 'income' ? 'text-success' : 'text-danger'"
                            :style="{ fontWeight: transaction.type === 'expense' && transaction.amount >= 500 ? 'bold' : 'normal' }">
                            {{ formatCurrency(transaction.amount) }}
                        </td>
                        <td>
                            <span :class="`badge ${transaction.type === 'income' ? 'bg-success' : 'bg-danger'}`">
                                {{ transaction.type.toUpperCase() }}
                            </span>
                        </td>
                        <td>{{ formatDate(transaction.date) }}</td>
                        <td>
                            <button class="btn btn-sm btn-danger" @click="deleteTransaction(index)">
                                <i class="fas fa-trash"></i> Delete
                            </button>
                        </td>
                    </tr>
                    <tr v-if="filteredTransactions.length === 0">
                        <td colspan="6" class="text-center text-muted">No transactions recorded yet.</td>
                    </tr>
                </tbody>
            </table>
        </div>
        <nav class="d-flex justify-content-center mt-3" v-if="totalPages > 1">
            <ul class="pagination mb-0">
                <li class="page-item" :class="{ disabled: currentPage === 1 }">
                    <button class="page-link" @click="currentPage = 1">First</button>
                </li>
                <li class="page-item" :class="{ disabled: currentPage === 1 }">
                    <button class="page-link" @click="currentPage--">Previous</button>
                </li>
                <li class="page-item" v-for="page in totalPages" :key="page" :class="{ active: page === currentPage }">
                    <button class="page-link" @click="currentPage = page">{{ page }}</button>
                </li>
                <li class="page-item" :class="{ disabled: currentPage === totalPages }">
                    <button class="page-link" @click="currentPage++">Next</button>
                </li>
                <li class="page-item" :class="{ disabled: currentPage === totalPages }">
                    <button class="page-link" @click="currentPage = totalPages">Last</button>
                </li>
            </ul>
        </nav>
    </div>
</template>

<script>
export default {
    props: {
        transactions: {
            type: Array,
            required: true
        }
    },
    data() {
        return {
            filterType: "all",
            currentPage: 1,
            perPage: 5
        };
    },
    computed: {
        filteredTransactions() {
            let transactions = this.transactions;

            transactions.sort((a, b) => new Date(b.date) - new Date(a.date));

            if (this.filterType !== "all") {
                transactions = transactions.filter(txn => txn.type === this.filterType);
            }

            return transactions;
        },

        paginatedTransactions() {
            const start = (this.currentPage - 1) * this.perPage;
            const end = start + this.perPage;
            return this.filteredTransactions.slice(start, end);
        },

        totalPages() {
            return Math.ceil(this.filteredTransactions.length / this.perPage);
        }
    },
    methods: {
        deleteTransaction(index) {
            const globalIndex = (this.currentPage - 1) * this.perPage + index;
            this.$emit("delete-transaction", globalIndex);
        },
        formatCurrency(amount) {
            return new Intl.NumberFormat("en-US", {
                style: "currency",
                currency: "USD"
            }).format(amount);
        },
        formatDate(date) {
            return new Date(date).toLocaleDateString("en-US", {
                year: "numeric",
                month: "short",
                day: "numeric"
            });
        }
    },
    watch: {
        filterType() {
            this.currentPage = 1;
        }
    }
};
</script>

<style scoped>
.table th,
.table td {
    text-align: center;
    vertical-align: middle;
}

.table-hover tbody tr:hover {
    background-color: rgba(0, 0, 0, 0.05);
}

@media (max-width: 768px) {
    .table-responsive {
        overflow-x: auto;
    }
}

.pagination {
    margin: 0;
}

.page-item.disabled .page-link {
    pointer-events: none;
    opacity: 0.6;
}

.page-item.active .page-link {
    background-color: #007bff;
    border-color: #007bff;
    color: #fff;
}

.page-link {
    color: #007bff;
    cursor: pointer;
}

.d-flex.justify-content-between {
    gap: 1rem;
}
</style>