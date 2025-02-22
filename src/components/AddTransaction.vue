<template>
    <div class="container mt-4">
        <h2 class="mb-3 text-center">Add Transaction</h2>

        <form @submit.prevent="handleSubmit" class="p-3 bg-light rounded shadow">
            <div class="row g-3 align-items-center">
                <div class="col-md-4">
                    <label class="form-label mb-1">Title</label>
                    <input type="text" class="form-control" v-model.trim="title" placeholder="Enter title" />
                    <small v-if="errors.title" class="text-danger">{{ errors.title }}</small>
                </div>
                <div class="col-md-3">
                    <label class="form-label mb-1">Amount</label>
                    <input type="number" class="form-control" v-model.number="amount" placeholder="Amount" min="0" />
                    <small v-if="errors.amount" class="text-danger">{{ errors.amount }}</small>
                </div>
                <div class="col-md-3">
                    <label class="form-label mb-1">Type</label>
                    <select class="form-select" v-model="type">
                        <option value="">Select Type</option>
                        <option value="income">Income</option>
                        <option value="expense">Expense</option>
                    </select>
                    <small v-if="errors.type" class="text-danger">{{ errors.type }}</small>
                </div>
                <div class="col-md-2 text-center">
                    <button type="submit" class="btn btn-primary w-100 mt-4">Add</button>
                </div>
            </div>
        </form>
    </div>
</template>

<script>
export default {
    data() {
        return {
            title: "",
            amount: null,
            type: "",
            errors: {}
        };
    },
    methods: {
        validateForm() {
            this.errors = {};

            if (!this.title) {
                this.errors.title = "Title is required";
            }

            if (!this.amount || this.amount < 0) {
                this.errors.amount = "Amount must be a positive number";
            }

            if (!this.type) {
                this.errors.type = "Transaction type is required";
            }

            return Object.keys(this.errors).length === 0;
        },
        handleSubmit() {
            // Validate the form
            if (!this.validateForm()) return;

            const newTransaction = {
                title: this.title,
                amount: this.amount,
                type: this.type,
                date: new Date().toISOString()
            };

            this.$emit("add-transaction", newTransaction);

            this.resetForm();
        },
        resetForm() {
            this.title = "";
            this.amount = null;
            this.type = "";
            this.errors = {};
        }
    }
};
</script>

<style scoped>
.form-control,
.form-select {
    border-radius: 10px;
    padding: 8px;
    font-size: 1rem;
}

.bg-light {
    background: #ffffff;
    border-radius: 10px;
    box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.1);
}

@media (max-width: 768px) {
    .row {
        flex-direction: column;
    }

    .col-md-2 {
        text-align: center;
    }

    .btn {
        width: 100%;
    }
}
</style>