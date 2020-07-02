<template>
    <div class="account-container">
        <ul class="account-data-list" v-for="(account, index) in accounts" :key="index">
            <li><label>Name: </label>{{account.LastName}}, {{account.FirstName}}</li>
            <li><label>Email: </label>{{account.Email}}</li>
            <li><label>Phone Number: </label>{{formatPhoneNumber(account.PhoneNumber)}}</li> 
            <li><label>Amount Due: </label>{{formatAmount(account.AmountDue)}}</li>
            <li v-if="showDate(account.PaymentDueDate)"><label>Due Date: </label>{{formatDate(account.PaymentDueDate)}}</li>
        </ul>
    </div>
</template>

<script>
    export default {
        name: 'user-accounts',
        props: {
            accounts: { required: true, type: Array },
        },
        methods: {
            showDate(date) {
                return date != undefined;
            },
            formatPhoneNumber(number) {
                //format number to (###)-###-####
                return number.replace(/(\d{3})(\d{3})(\d{4})/, '($1)-$2-$3');
            },
            formatDate(date) {
                //format date to (##/##/####)
                var temp = new Date(date);
                let formatted_date = (temp.getMonth() + 1) + "/" + temp.getDate() + "/" + temp.getFullYear();
                return formatted_date;
            },
            formatAmount(amount) {
                //format amount to "$###.##" or "$##.##"
                var formatter = new Intl.NumberFormat('en-US', {
                  style: 'currency',
                  currency: 'USD',
                });

                return formatter.format(amount);
            }
        }
    };
</script>

<style scoped>
</style>

