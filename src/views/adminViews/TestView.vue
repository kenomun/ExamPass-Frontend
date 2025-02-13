<template>
  <div class="container mx-auto p-6">
    <h1 class="text-3xl font-bold text-gray-800 text-center mb-6">Lista de Tests</h1>

    <FormButtonComponent class="mt-6" buttonText="Crear Test" formTitle="Nuevo Test" formButtonText="Crear test"
      :fields="['name', 'email']" :entity="{}" @openForm="openFormCreate" />

    <Table class="mt-6" :headers="headers" :rows="rows" :options="option" @actionSelect="handleAction"
      :actions="true" />

    <button type="button" @click="goBack"
      class="mt-10 text-white bg-gray-800 hover:bg-gray-900 focus:outline-none focus:ring-4 focus:ring-gray-300 font-medium rounded-full text-sm px-5 py-2.5 me-2 mb-2 dark:bg-gray-800 dark:hover:bg-gray-700 dark:focus:ring-gray-700 dark:border-gray-700">Volver</button>

    <!-- Componente de Toast -->
    <Toast v-if="toastVisible" :message="toastMessage" :type="toastType" :duration="3000"
      @closed="toastVisible = false" />

    <!-- Modal de Confirmación de Eliminación -->
    <AlertModal v-if="alertVisible" :show="alertVisible" title="Eliminar Test"
      message="¿Estás seguro de que deseas eliminar este test? Esta acción no se puede deshacer."
      @confirm="confirmDeleteTest" @cancel="cancelDeleteTest" />

    <TestComponentForm v-if="formModalVisible" :actionType="actionType" :isOpen="formModalVisible"
      :formTitle="formTitle" :formButtonText="formButtonText" :testToEdit="testToEdit" @save="handleSave"
      @cancel="handleCancel" :testId="testId" />

  </div>
</template>

<script>
import axios from "axios";
import Table from "../../components/Table.vue";
import Toast from "../../components/ToastComponent.vue";
import AlertModal from "../../components/AlertModal.vue";
import FormButtonComponent from "../../components/FormButtonComponent.vue";
import TestComponentForm from "../../components/TestCompontForm.vue"

import { API_BASE_URL } from "../../config/config";

export default {
  name: "TestListView",
  components: {
    Table,
    Toast,
    AlertModal,
    FormButtonComponent,
    TestComponentForm
  },
  data() {
    return {
      tests: [],
      rows: [],
      headers: [],
      testToDelete: null,
      alertVisible: false,
      toastVisible: false,
      actionType: null,
      testId: null,
      toastMessage: "",
      toastType: "success",
      formModalVisible: false,
      formTitle: 'Crear Test',
      formButtonText: 'Crear',
      testToEdit: null,
      modalButtons: [
        { label: "Cerrar", class: "bg-gray-300 text-gray-800", action: "cancel" },
        { label: "Guardar", class: "bg-blue-500 text-white", action: "confirm" },
      ],
      option: [
        { label: "Editar", class: "ml-2 bg-green-500 text-white px-3 py-1 rounded hover:bg-green-600 transition duration-300 mr-2", action: "edit" },
        { label: "Eliminar", class: "bg-red-500 text-white px-3 py-1 rounded hover:bg-red-600 transition duration-300", action: "delete" },
        { label: "Ver Detalles", class: "ml-2 bg-gray-500 text-white px-3 py-1 rounded hover:bg-gray-600 transition duration-300 mr-2", action: "view" },
      ],
      subjects: [
        { id: 1, name: "Matemáticas" },
        { id: 2, name: "Lengua y Literatura" },
        { id: 3, name: "Ciencias" },
        { id: 4, name: "Historia" },
        { id: 5, name: "Geografía" },
        { id: 6, name: "Inglés" },
        { id: 7, name: "Arte" },
        { id: 8, name: "Educación Física" },
      ],
    };
  },
  async created() {
    try {
      const response = await axios.get(`${API_BASE_URL}/tests`);
      this.tests = response.data;

      // Determina dinámicamente los encabezados en base a los datos
      this.headers = [
        { key: "name", label: "Nombre del Test" },
        { key: "subjectName", label: "Asignatura" }
      ];

      // Transforma los datos de los tests en el formato necesario
      this.rows = this.tests.map((test) => ({
        id: test.id,
        name: test.name,
        subjectName: test.Subject.name
      }));
    } catch (error) {
      console.error("Error al obtener los tests:", error);
    }
  },

  methods: {
    goBack() {
      this.$router.go(-1); // Esto te lleva a la vista anterior
    },

    async handleAction(data, actionId) {
      if (actionId === 1) {
        const response = await axios.get(`${API_BASE_URL}/test/${data.id}`);
        const test = response.data;
        this.openFormCreate(test, 'edit');
      } else if (actionId === 2) {
        this.$router.push({ name: "testDetail", params: { id: data.id } });
      } else if (actionId === 3) {
        this.handleDeleteTest(data);
      }
    },

    openFormCreate(data = null, action = "create") {
      this.actionType = action;
      if (action === "edit") {
        this.formTitle = "Editar Test";
        this.testId = data.id;
        // Formatear las preguntas y respuestas
        this.testToEdit = {
          name: data.name || '',
          subjectId: data.subjectId || null,
          content: data.content || '',
          questions: data.Questions.map((question) => ({
            id: question.id,
            questionText: question.question,
            answers: question.Answers.map(answer => answer.answer),
            correctAnswer: question.Answers.findIndex(answer => answer.isCorrect),
            content: question.content ? question.content.description : ''
          }))
        };
      } else {
        this.testToEdit = {
          name: '',
          subjectId: null,
          questions: [
            { id:0, questionText: '', answers: ['', '', '', ''], correctAnswer: 0 },
            { id:0, questionText: '', answers: ['', '', '', ''], correctAnswer: 0 },
            { id:0, questionText: '', answers: ['', '', '', ''], correctAnswer: 0 },
            { id:0, questionText: '', answers: ['', '', '', ''], correctAnswer: 0 },
            { id:0, questionText: '', answers: ['', '', '', ''], correctAnswer: 0 },
          ],
        }
      };
      this.formModalVisible = true;
    },

    handleDeleteTest(test) {
      this.testToDelete = test;
      this.alertVisible = true;
    },
    async confirmDeleteTest() {
      try {
        const response = await axios.delete(`${API_BASE_URL}/test/${this.testToDelete.id}`);
        if (response.status == 200) {
          this.alertVisible = false;
          this.toastMessage = `Test "${this.testToDelete.name}" eliminado correctamente.`;
          this.toastType = "success";
          this.loadTest()
          this.toastVisible = true;
        } else {
          this.alertVisible = false;
          this.toastMessage = `Ocurrió un error al intentar eliminar el test.`;
          this.toastType = "error";
          this.loadTest()
          this.toastVisible = true;
        }

      } catch (error) {
        this.toastMessage = "Ocurrió un error al intentar eliminar el test.";
        this.toastType = "error";
        this.toastVisible = true;
      }
    },

    async handleSave(testData, actionType, testId = null) {
      try {

        if (actionType === 'edit') {
          const response = await axios.put(`${API_BASE_URL}/test/${testId}`, testData);
          this.toastMessage = `Test actualizado correctamente.`;
          this.toastType = "success";
          this.toastVisible = true;
          await this.loadTest();
          this.formModalVisible = false;
        } else {
          const payload = {
            testName: testData.testName,
            subjectId: testData.subjectId,
            questions: testData.questions.map(question => ({
              question: question.question,
              content: { description: question.content.description || '' },
              answers: question.answers.map((answer, index) => ({
                answer: answer.answer,
                isCorrect: question.correctAnswer === index
              }))
            }))
          };

          const response = await axios.post(`${API_BASE_URL}/tests`, payload);

          if (response.status == 200) {
            await this.loadTest();
            this.toastMessage = `Test  guardado correctamente.`;
            this.toastType = "success";
            this.toastVisible = true;
            this.formModalVisible = false;
          } else {
            this.toastMessage = `Error al guardar Test.`;
            this.toastType = "error";
            await this.loadTest();
            this.toastVisible = true;

            // this.formModalVisible = false;

          }
        }

      } catch (error) {
        this.toastMessage = "Error al guardar Test.";
        this.toastType = "error";
        this.toastVisible = true;
      }
    },
    cancelDeleteTest() {
      this.alertVisible = false;
    },


    closeModal() {
      this.modalVisible = false;
    },
    handleConfirmTest(formData) {
      this.modalVisible = false;
    },

    handleCancel() {
      this.formModalVisible = false;
    },


    async loadTest() {
      try {
        const response = await axios.get(`${API_BASE_URL}/tests`);
        this.tests = response.data;

        // Determina dinámicamente los encabezados en base a los datos
        this.headers = [
          { key: "name", label: "Nombre del Test" },
          { key: "subjectName", label: "Asignatura" }
        ];

        // Transforma los datos de los tests en el formato necesario
        this.rows = this.tests.map((test) => ({
          id: test.id,
          name: test.name,
          subjectName: test.Subject.name
        }));
      } catch (error) {
        console.error("Error al obtener los tests:", error);
      }
    },

  }
};
</script>

<style scoped>
/* Estilos personalizados si es necesario */
</style>