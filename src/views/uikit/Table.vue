<template>
    <div class="grid">
        <div class="col-12">
            <div class="card">
                <h5>Punto de Venta (POS)</h5>
                <di class="col-12">
                    <div class="card">
                        <div class="flex justify-content-between flex-column sm:flex-row">
                            <span class="p-float-label">
                            <InputText id="Producto" v-model="nombreProd" type="text"/>
                            <label for="Producto">Nombre del Producto:</label>
                            </span>
                            <span class="p-float-label">
                                <InputText id="Cantidad" type="text" v-model="cantidad" />
                                <label for="Cantidad">Cantidad</label>
                            </span>
                            <span class="p-float-label">
                                <InputText id="Precio" type="text" v-model="preciosU"/>
                                <label for="Precio">$ Precio U.</label>
                            </span>
                            <br>
                            <Button type="button" icon="pi pi-plus" label="Registrar" severity="success"
                                @Click="RegistrarV" />
                        </div>
                    </div>
                </di>
                <DataTable :value="datos" showGridlines paginator :rows="5" :rowsPerPageOptions="[5, 10, 20, 50]"
                    tableStyle="min-width: 50rem">
                    <Column field="CNS" header="Cns" sortable style="width: 20%" />
                    <Column field="nombre" header="Nombre del Producto" />
                    <Column field="precio" header="Precio U." />
                    <Column field="cantidad" header="Cantidad" />
                    <Column field="preciosP" header="Precio P." />
                    <Column header="Acciones" :exportable="false" style="min-width:8rem">
                        <template #body="slotProps">
                            <Button icon="pi pi-pencil" outlined rounded class="mr-2"
                                @click="editProduct(slotProps.data)" />
                            <Button icon="pi pi-trash" outlined rounded severity="danger"
                                @click="confirmDeleteProduct(slotProps.data)" />
                        </template>
                    </Column>
                </DataTable>
                <di class="col-12">
                    <div class="card">
                        <label for="apepat">Subtotal:</label>
                        <InputText id="subtotal1" type="text" v-model="subtotal" readonly />
                        <label for="apepat">IVA (16%):</label>
                        <InputText id="iva1" type="text" v-model="iva" readonly />
                        <label for="apepat">Total:</label>
                        <InputText id="total1" type="text" v-model="total" readonly />
                    </div>
                </di>
                <Dialog v-model:visible="productDialog" :style="{ width: '450px' }" header="Detalles del Producto"
                    :modal="true" class="p-fluid">

                    <div class="field">
                        <label for="name">Nombre del Producto</label>
                        <InputText v-model="selectedProduct.nombre" type="text" placeholder="Nombre del Producto..." />
                    </div>
                    <div class="field">
                        <label for="cantidad">Cantidad</label>
                        <InputNumber v-model="selectedProduct.cantidad" placeholder="Cantidad" />
                    </div>
                    <div class="field">
                        <label for="preciosU">Precios U.</label>
                        <InputNumber v-model="selectedProduct.precio" placeholder="$ Precio U." :minFractionDigits="2"
                            :maxFractionDigits="5" />
                    </div>
                    <template #footer>
                        <Button label="Save" icon="pi pi-check" text @click="saveProduct" />
                    </template>
                </Dialog>
            </div>
        </div>
    </div>
</template>
<script>
import { defineComponent } from 'vue';

export default defineComponent({
    data() {
        return {
            productDialog: false,
            datos: [],
            cns: 1,
            nombreProd: '',
            subtotal: 0,
            iva: 0,
            total: 0,
            cantidad: '',
            preciosU: '',
            mensaje: 'Tienes campos vacios',
            selectedProduct: null
        };
    },
    methods: {
        RegistrarV() {
            if (this.nombreProd === '' || this.cantidad === '' || this.preciosU === '') {
                alert(this.mensaje);
            } else {
                const producto = {
                    CNS: this.cns++,
                    nombre: this.nombreProd,
                    cantidad: this.cantidad,
                    precio: this.preciosU,
                    preciosP: this.preciosU * this.cantidad
                };
                this.datos.push(producto);
                this.nombreProd = '';
                this.cantidad = '';
                this.preciosU = '';
                this.sumarCantidad();
            }
        },
        sumarCantidad() {

            let suma = 0;
            for (let i = 0; i < this.datos.length; i++) {
                const producto = this.datos[i];
                suma += producto.preciosP;
            }
            const iva = suma * 0.16;
            const total = suma + iva;
            this.subtotal = suma;
            this.iva = iva;
            this.total = total;
        },
        editProduct(producto) {
            this.selectedProduct = producto;
            this.productDialog = true;
            this.selectedProduct.preciosP = this.selectedProduct.precio * this.selectedProduct.cantidad;

        },
        confirmDeleteProduct(producto) {
            const index = this.datos.indexOf(producto);
            if (index >= 0) {
                this.datos.splice(index, 1);
                this.sumarCantidad();
            }

        },
        saveProduct() {
            for (let i = 0; i < this.datos.length; i++) {
                if (this.datos[i].CNS === this.selectedProduct.CNS) {
                    this.datos[i].nombre = this.selectedProduct.nombre;
                    this.datos[i].cantidad = this.selectedProduct.cantidad;
                    this.datos[i].precio = this.selectedProduct.precio;
                    this.datos[i].preciosP = this.selectedProduct.precio * this.selectedProduct.cantidad;
                    //break;
                }
            }
            this.sumarCantidad();
            this.productDialog = false;
        },
    }
})
</script>


<style scoped lang="scss">
@import '@/assets/demo/styles/badges.scss';

::v-deep(.p-datatable-frozen-tbody) {
    font-weight: bold;
}

::v-deep(.p-datatable-scrollable .p-frozen-column) {
    font-weight: bold;
}
</style>
