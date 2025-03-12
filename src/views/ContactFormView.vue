<template>
    <div class="page">
        <h4>{{ isEditMode ? "Hiệu chỉnh Liên hệ" : "Thêm mới Liên hệ" }}</h4>
        <ContactForm :contact="contact" @submit:contact="saveContact" @delete:contact="deleteContact" />
        <p>{{ message }}</p>
    </div>
</template>

<script>
import ContactForm from "@/components/ContactForm.vue";
import ContactService from "@/services/contact.service";

export default {
    components: { ContactForm },
    props: {
        id: { type: String, required: false }, // Không bắt buộc (nếu thêm mới sẽ không có id)
    },
    data() {
        return {
            contact: {
                name: "",
                email: "",
                address: "",
                phone: "",
                favorite: false,
            },
            message: "",
        };
    },
    computed: {
        isEditMode() {
            return !!this.id; // Nếu có id, đang ở chế độ chỉnh sửa
        },
    },
    methods: {
        async getContact(id) {
            try {
                this.contact = await ContactService.get(id);
            } catch (error) {
                console.error(error);
                this.$router.push({ name: "notfound" });
            }
        },
        async saveContact(data) {
            try {
                console.log(this.isEditMode);
                if (this.isEditMode) {
                    await ContactService.update(this.id, data);
                    alert("Liên hệ được cập nhật thành công.");
                } else {
                    await ContactService.create(data);
                    alert("Liên hệ được thêm mới thành công.");
                }
                this.$router.push({ name: "contactbook" });
            } catch (error) {
                console.error(error);
            }
        },
        async deleteContact() {
            if (!this.isEditMode) return;
            if (confirm("Bạn muốn xóa Liên hệ này?")) {
                try {
                    await ContactService.delete(this.id);
                    this.$router.push({ name: "contactbook" });
                } catch (error) {
                    console.error(error);
                }
            }
        },
    },
    created() {
        if (this.isEditMode) this.getContact(this.id);
    },
};
</script>
