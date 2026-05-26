<template>
    <div
        class="min-h-screen bg-slate-50 font-sans text-slate-800 antialiased selection:bg-blue-500 selection:text-white pb-20">

        <!-- Header Fijo con Estilo de App -->
        <header class="sticky top-0 z-30 bg-white/80 backdrop-blur-md border-b border-slate-100 px-4 py-3 shadow-sm">
            <div class="flex items-center justify-between max-w-md mx-auto">
                <h1 class="text-lg font-black tracking-tight text-slate-900 uppercase">
                    Pasacalle <span class="text-blue-600">Control</span>
                </h1>
                <div class="flex gap-2">
                    <span
                        class="flex items-center gap-1 text-[10px] font-bold bg-blue-100 text-blue-700 px-2 py-1 rounded-full">
                        B1: {{ pasajerosBus1.length }}
                    </span>
                    <span
                        class="flex items-center gap-1 text-[10px] font-bold bg-purple-100 text-purple-700 px-2 py-1 rounded-full">
                        B2: {{ pasajerosBus2.length }}
                    </span>
                </div>
            </div>
        </header>

        <main class="mx-auto max-w-md p-4">
            <!-- NAVEGACIÓN POR TABS (Sticky debajo del header) -->
            <nav
                class="flex p-1.5 bg-slate-200/50 rounded-2xl mb-6 sticky top-16 z-20 backdrop-blur-sm border border-white/50 shadow-inner">
                <button @click="tabActiva = 'registro'"
                    :class="tabActiva === 'registro' ? 'bg-white text-blue-600 shadow-md scale-100' : 'text-slate-500 scale-95'"
                    class="flex-1 flex flex-col items-center justify-center py-2.5 rounded-xl font-bold transition-all duration-200">
                    <UserPlusIcon class="w-5 h-5" />
                    <span class="text-[10px] mt-1 uppercase tracking-tight">Registro</span>
                </button>
                <button @click="tabActiva = 'bus1'"
                    :class="tabActiva === 'bus1' ? 'bg-white text-blue-600 shadow-md scale-100' : 'text-slate-500 scale-95'"
                    class="flex-1 flex flex-col items-center justify-center py-2.5 rounded-xl font-bold transition-all duration-200">
                    <TruckIcon class="w-5 h-5" />
                    <span class="text-[10px] mt-1 uppercase tracking-tight">Bus 01</span>
                </button>
                <button @click="tabActiva = 'bus2'"
                    :class="tabActiva === 'bus2' ? 'bg-white text-purple-600 shadow-md scale-100' : 'text-slate-500 scale-95'"
                    class="flex-1 flex flex-col items-center justify-center py-2.5 rounded-xl font-bold transition-all duration-200">
                    <TruckIcon class="w-5 h-5" />
                    <span class="text-[10px] mt-1 uppercase tracking-tight">Bus 02</span>
                </button>
            </nav>

            <!-- CONTENIDO DINÁMICO CON TRANSICIONES -->
            <Transition name="fade" mode="out-in">
                <!-- VISTA: REGISTRO -->
                <section v-if="tabActiva === 'registro'" key="registro" class="space-y-4">
                    <div class="rounded-3xl bg-white p-6 shadow-xl shadow-slate-200/50 border border-slate-100">
                        <div class="mb-6 flex items-center gap-3">
                            <div :class="editandoPasajeroId ? 'bg-amber-100 text-amber-600' : 'bg-blue-100 text-blue-600'"
                                class="p-3 rounded-2xl">
                                <UserPlusIcon v-if="!editandoPasajeroId" class="w-6 h-6" />
                                <PencilSquareIcon v-else class="w-6 h-6" />
                            </div>
                            <div>
                                <h2 class="text-xl font-bold text-slate-900 leading-none">
                                    {{ editandoPasajeroId ? 'Editar Pasajero' : 'Nuevo Registro' }}
                                </h2>
                                <p class="text-xs text-slate-500 mt-1 uppercase font-semibold tracking-tighter">Ingrese
                                    datos del pasajero</p>
                            </div>
                        </div>

                        <form @submit.prevent="guardarPasajero" class="space-y-5">
                            <div class="space-y-1.5">
                                <label class="text-[11px] font-black uppercase text-slate-400 ml-1">Nombre
                                    Completo</label>
                                <input v-model="formulario.nombre" type="text" required placeholder="Ej. JUAN PEREZ"
                                    class="w-full rounded-2xl border-slate-200 bg-slate-50 px-5 py-4 text-base font-bold outline-none transition focus:ring-4 focus:ring-blue-500/10 focus:bg-white" />
                            </div>

                            <div class="grid grid-cols-2 gap-4">
                                <div class="space-y-1.5">
                                    <label class="text-[11px] font-black uppercase text-slate-400 ml-1">Teléfono</label>
                                    <input v-model="formulario.telefono" type="tel" placeholder="9..."
                                        class="w-full rounded-2xl border-slate-200 bg-slate-50 px-5 py-4 text-base font-bold outline-none transition focus:bg-white" />
                                </div>
                                <div class="space-y-1.5">
                                    <label
                                        class="text-[11px] font-black uppercase text-slate-400 ml-1 text-center block">Unidad</label>
                                    <div class="grid grid-cols-2 gap-2 bg-slate-100 p-1.5 rounded-2xl h-[60px]">
                                        <button type="button" @click="formulario.bus_asignado = 1"
                                            :class="formulario.bus_asignado === 1 ? 'bg-white text-blue-600 shadow-sm' : 'text-slate-400'"
                                            class="rounded-xl text-xs font-black transition-all">B1</button>
                                        <button type="button" @click="formulario.bus_asignado = 2"
                                            :class="formulario.bus_asignado === 2 ? 'bg-white text-purple-600 shadow-sm' : 'text-slate-400'"
                                            class="rounded-xl text-xs font-black transition-all">B2</button>
                                    </div>
                                </div>
                            </div>

                            <div class="space-y-1.5">
                                <label class="text-[11px] font-black uppercase text-slate-400 ml-1">Nota /
                                    Equipaje</label>
                                <input v-model="formulario.nota" type="text" placeholder="Ej. 2 maletas grandes"
                                    class="w-full rounded-2xl border-slate-200 bg-slate-50 px-5 py-4 text-sm font-medium outline-none transition focus:bg-white" />
                            </div>

                            <div class="flex flex-col gap-3 pt-4">
                                <button type="submit" :disabled="guardando || !formulario.nombre.trim()"
                                    :class="editandoPasajeroId ? 'bg-amber-500 shadow-amber-200' : 'bg-blue-600 shadow-blue-200'"
                                    class="w-full rounded-2xl py-5 font-black text-white shadow-lg transition active:scale-95 disabled:opacity-50 flex items-center justify-center gap-2">
                                    <component
                                        :is="guardando ? 'span' : (editandoPasajeroId ? CheckCircleIcon : UserPlusIcon)"
                                        class="w-6 h-6" />
                                    {{ guardando ? 'Guardando...' : (editandoPasajeroId ? 'ACTUALIZAR DATOS' :
                                    'REGISTRAR AHORA') }}
                                </button>
                                <button v-if="editandoPasajeroId" type="button" @click="cancelarEdicion"
                                    class="w-full py-4 text-slate-400 font-bold text-sm hover:text-slate-600">
                                    Cancelar Edición
                                </button>
                            </div>
                        </form>
                    </div>
                </section>

                <!-- VISTA: LISTADOS (BUS 1 O BUS 2) -->
                <section v-else key="listas" class="space-y-4">
                    <!-- Buscador flotante -->
                    <div class="relative group mx-2">
                        <MagnifyingGlassIcon class="absolute left-4 top-1/2 -translate-y-1/2 w-5 h-5 text-slate-400" />
                        <input v-model="filtroNombre" type="text" :placeholder="'Buscar en Bus 0' + tabActiva.at(-1)"
                            class="w-full pl-12 pr-4 py-4 rounded-2xl border-none bg-white shadow-lg shadow-slate-200/50 outline-none font-bold text-slate-700" />
                    </div>

                    <!-- Lista Estilo Card -->
                    <div class="space-y-3 px-1">
                        <TransitionGroup name="list">
                            <div v-for="p in pasajerosFiltrados" :key="p.id"
                                class="bg-white p-4 rounded-3xl shadow-sm border border-slate-100 flex items-center gap-4 active:scale-[0.98] transition-transform">
                                <div :class="tabActiva === 'bus1' ? 'bg-blue-50 text-blue-600' : 'bg-purple-50 text-purple-600'"
                                    class="w-12 h-12 rounded-2xl flex items-center justify-center flex-shrink-0 font-black text-xl">
                                    {{ p.nombre_completo.charAt(0) }}
                                </div>
                                <div class="flex-1 min-w-0">
                                    <h4 class="font-bold text-slate-900 truncate text-base leading-tight">{{
                                        p.nombre_completo }}</h4>
                                    <div class="flex flex-wrap gap-2 mt-1">
                                        <span v-if="p.telefono"
                                            class="flex items-center gap-1 text-[10px] font-bold text-green-600 bg-green-50 px-2 py-0.5 rounded-md">
                                            <PhoneIcon class="w-3 h-3" /> {{ p.telefono }}
                                        </span>
                                        <span v-if="p.nota"
                                            class="flex items-center gap-1 text-[10px] font-bold text-slate-500 bg-slate-100 px-2 py-0.5 rounded-md truncate max-w-[150px]">
                                            <DocumentTextIcon class="w-3 h-3" /> {{ p.nota }}
                                        </span>
                                    </div>
                                </div>
                                <div class="flex items-center gap-1">
                                    <button @click="editarPasajero(p)"
                                        class="p-2.5 text-blue-600 bg-blue-50 rounded-xl">
                                        <PencilSquareIcon class="w-5 h-5" />
                                    </button>
                                    <button @click="eliminarPasajero(p.id)"
                                        class="p-2.5 text-red-500 bg-red-50 rounded-xl">
                                        <TrashIcon class="w-5 h-5" />
                                    </button>
                                </div>
                            </div>
                        </TransitionGroup>

                        <div v-if="pasajerosFiltrados.length === 0" class="py-20 text-center">
                            <UserGroupIcon class="w-12 h-12 text-slate-200 mx-auto mb-2" />
                            <p class="text-slate-400 font-bold text-xs uppercase tracking-widest">No hay pasajeros</p>
                        </div>
                    </div>
                </section>
            </Transition>
        </main>

        <!-- Componente de Notificación Global -->
        <Notification :message="notificationState.message" :type="notificationState.type" :show="notificationState.show"
            @close="hideNotification" />
    </div>
</template>

<script setup>
import { ref, onMounted, computed, watch } from 'vue'
import { createClient } from '@supabase/supabase-js'
// Importar iconos de Heroicons
import {
    CheckCircleIcon,
    UserPlusIcon,
    TruckIcon,
    PencilSquareIcon,
    TrashIcon,
    PhoneIcon,
    UserGroupIcon,
    DocumentTextIcon,
    MagnifyingGlassIcon
} from '@heroicons/vue/24/outline'

// Configuración de Supabase
const supabaseUrl = 'https://jyygnbrpckoprmkokdzx.supabase.co'
const supabaseAnonKey = 'sb_publishable_aam2ExVKQe3INigeLt4cEg_8ue4L6zm'
const supabase = createClient(supabaseUrl, supabaseAnonKey)

// Importar componentes y funciones de notificación
import Notification from '@/components/shared/Notification.vue'
import { notificationState, showNotification, hideNotification } from '@/events/notificationEvents.ts'

// Estados reactivos
const pasajeros = ref([])
const guardando = ref(false) // Indica si una operación de guardado/actualización está en curso
const tabActiva = ref('registro') // 'registro', 'bus1', 'bus2'
const filtroNombre = ref('') // Para el buscador
const formulario = ref({
    nombre: '',
    telefono: '',
    bus_asignado: 1,
    nota: ''
})

const editandoPasajeroId = ref(null) // Almacena el ID del pasajero que se está editando
// Filtrar pasajeros por cada bus de forma eficiente
const pasajerosBus1 = computed(() => {
    return pasajeros.value
        .filter(p => p.bus_asignado === 1)
        .sort((a, b) => new Date(b.creado_en).getTime() - new Date(a.creado_en).getTime())
})
const pasajerosBus2 = computed(() => {
    return pasajeros.value
        .filter(p => p.bus_asignado === 2)
        .sort((a, b) => new Date(b.creado_en).getTime() - new Date(a.creado_en).getTime())
})

// Lógica de búsqueda combinada con la pestaña activa
const pasajerosFiltrados = computed(() => {
    const listaBase = tabActiva.value === 'bus1' ? pasajerosBus1.value : pasajerosBus2.value
    if (!filtroNombre.value.trim()) return listaBase

    const query = filtroNombre.value.toLowerCase()
    return listaBase.filter(p =>
        p.nombre_completo.toLowerCase().includes(query)
    )
})

// Obtener la lista inicial de pasajeros de la base de datos
const cargarPasajeros = async () => {
    const { data, error } = await supabase
        .from('pasajeros')
        .select('*')
        .order('creado_en', { ascending: false })

    if (!error) pasajeros.value = data
}

// Escuchar inserciones en Tiempo Real
const activarTiempoReal = () => {
    supabase
        .channel('cambios-pasajeros')
        .on('postgres_changes', { event: '*', schema: 'public', table: 'pasajeros' }, (payload) => {
            const { eventType, new: newRow, old: oldRow } = payload
            console.log('Cambio detectado:', eventType, payload)

            if (eventType === 'INSERT') {
                pasajeros.value = [newRow, ...pasajeros.value]
            } else if (eventType === 'UPDATE') {
                const index = pasajeros.value.findIndex(p => p.id === newRow.id)
                if (index !== -1) {
                    // Usamos splice para asegurar que Vue detecte el cambio de objeto profundo
                    pasajeros.value.splice(index, 1, newRow)
                }
            } else if (eventType === 'DELETE') {
                pasajeros.value = pasajeros.value.filter(p => p.id !== oldRow.id)
            }
        })
        .subscribe()
}

// Función para limpiar y resetear el formulario
const resetFormulario = () => {
    formulario.value = {
        nombre: '',
        telefono: '',
        bus_asignado: formulario.value.bus_asignado, // Mantiene el bus seleccionado
        nota: ''
    }
    editandoPasajeroId.value = null
}

// Guardar (insertar o actualizar) un pasajero
const guardarPasajero = async () => {
    if (guardando.value) return
    guardando.value = true

    // Validar que el nombre no esté vacío
    if (!formulario.value.nombre.trim()) {
        showNotification('El nombre del pasajero no puede estar vacío.', 'alert-warning')
        guardando.value = false
        return
    }

    const nombreLimpio = formulario.value.nombre
        .trim()
        .replace(/\s+/g, ' ') // Cambia múltiples espacios por uno solo
        .toUpperCase()        // Todo a mayúsculas para homogeneidad

    const datosAEnviar = {
        nombre_completo: nombreLimpio,
        bus_asignado: formulario.value.bus_asignado,
        telefono: formulario.value.telefono.trim() || null,
        nota: formulario.value.nota.trim() || null
    }

    let error = null

    if (editandoPasajeroId.value) {
        // Modo edición: Actualizar pasajero existente
        const { error: updateError } = await supabase
            .from('pasajeros')
            .update(datosAEnviar)
            .eq('id', editandoPasajeroId.value)
        error = updateError
    } else {
        // Modo registro: Insertar nuevo pasajero
        const { error: insertError } = await supabase
            .from('pasajeros')
            .insert([datosAEnviar])
        error = insertError
    }

    guardando.value = false

    if (error) {
        if (error.code === '23505') {
            showNotification(`El pasajero "${nombreLimpio}" ya está registrado.`, 'alert-warning')
        } else {
            showNotification(`Error al guardar pasajero: ${error.message}`, 'alert-error')
        }
        return
    }

    showNotification(`Pasajero ${editandoPasajeroId.value ? 'actualizado' : 'registrado'} con éxito.`, 'alert-success')
    resetFormulario()
}

// Cargar datos de un pasajero al formulario para edición
const editarPasajero = (pasajero) => {
    editandoPasajeroId.value = pasajero.id
    formulario.value = {
        nombre: pasajero.nombre_completo,
        telefono: pasajero.telefono || '',
        bus_asignado: pasajero.bus_asignado,
        nota: pasajero.nota || ''
    }
    // Cambiar a la pestaña de registro para editar
    tabActiva.value = 'registro'
    // Desplazar la vista al formulario
    window.scrollTo({ top: 0, behavior: 'smooth' })
}

// Cancelar el modo edición y limpiar el formulario
const cancelarEdicion = () => {
    resetFormulario()
    showNotification('Edición cancelada.', 'alert-info')
}

// Eliminar un pasajero
const eliminarPasajero = async (id) => {
    if (!confirm('¿Estás seguro de que quieres eliminar a este pasajero?')) {
        return
    }

    const { error } = await supabase
        .from('pasajeros')
        .delete()
        .eq('id', id)

    if (error) {
        showNotification(`Error al eliminar pasajero: ${error.message}`, 'alert-error')
        return
    }

    showNotification('Pasajero eliminado con éxito.', 'alert-success')
    // Si el pasajero eliminado era el que se estaba editando, cancelar edición
    if (editandoPasajeroId.value === id) {
        cancelarEdicion()
    }
}
// Iniciar procesos al montar la aplicación
onMounted(() => {
    cargarPasajeros()
    activarTiempoReal()
})
</script>

<style scoped>
/* Animaciones */
.list-enter-active,
.list-leave-active,
.fade-enter-active,
.fade-leave-active {
    transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

.list-enter-from,
.list-leave-to {
    opacity: 0;
    transform: translateY(10px);
}

.fade-enter-from,
.fade-leave-to {
    opacity: 0;
}

.list-move {
    transition: transform 0.3s ease;
}

/* Para evitar scroll horizontal en iOS */
html,
body {
    overflow-x: hidden;
    overscroll-behavior-y: contain;
}
</style>