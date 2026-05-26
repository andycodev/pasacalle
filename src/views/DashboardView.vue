<template>
    <div
        class="min-h-screen bg-slate-50 font-sans text-slate-800 antialiased selection:bg-orange-500 selection:text-white">

        <!-- Cabecera Fija (Header + Tabs) -->
        <div class="sticky top-0 z-30 bg-white/95 backdrop-blur-md border-b border-slate-200 shadow-sm">
            <header class="px-4 py-3 max-w-md mx-auto">
                <div class="max-w-md mx-auto">
                    <h1 class="text-2xl font-black tracking-tight text-slate-900 uppercase text-center">
                        Control de <span class="text-orange-600">pasajeros</span>
                    </h1>
                    <p class="text-xs text-slate-500 text-center font-bold mt-1 leading-tight px-4">
                        Registro de personas en respaldo al Dr. Roldán Díaz.
                    </p>
                    <div class="flex justify-center gap-3 mt-2">
                        <div
                            class="flex items-center bg-orange-600 text-white pl-2.5 pr-1 py-1 rounded-full shadow-lg shadow-orange-200">
                            <TruckIcon class="w-3.5 h-3.5 mr-1.5 opacity-80" />
                            <span class="text-[10px] font-black uppercase tracking-widest mr-2">Bus 1</span>
                            <div
                                class="bg-white text-orange-600 h-6 min-w-[24px] px-1.5 rounded-full flex items-center justify-center text-[11px] font-black">
                                {{ pasajerosBus1.length }}
                            </div>
                        </div>
                        <div
                            class="flex items-center bg-amber-500 text-white pl-2.5 pr-1 py-1 rounded-full shadow-lg shadow-amber-200">
                            <TruckIcon class="w-3.5 h-3.5 mr-1.5 opacity-80" />
                            <span class="text-[10px] font-black uppercase tracking-widest mr-2">Bus 2</span>
                            <div
                                class="bg-white text-amber-500 h-6 min-w-[24px] px-1.5 rounded-full flex items-center justify-center text-[11px] font-black">
                                {{ pasajerosBus2.length }}
                            </div>
                        </div>
                    </div>
                </div>
            </header>

            <!-- NAVEGACIÓN POR TABS -->
            <div class="px-4 pb-3 max-w-md mx-auto">
                <nav class="flex p-1 bg-slate-200/50 rounded-2xl border border-white/50 shadow-inner">
                    <button @click="tabActiva = 'registro'"
                        :class="tabActiva === 'registro' ? 'bg-white text-orange-600 shadow-md' : 'text-slate-500'"
                        class="flex-1 flex items-center justify-center gap-2 py-2.5 rounded-xl font-bold transition-all duration-200 text-sm">
                        <UserPlusIcon class="w-4 h-4" /> Registro
                    </button>
                    <button @click="tabActiva = 'bus1'"
                        :class="tabActiva === 'bus1' ? 'bg-white text-orange-600 shadow-md' : 'text-slate-500'"
                        class="flex-1 flex items-center justify-center gap-2 py-2.5 rounded-xl font-bold transition-all duration-200 text-sm">
                        <TruckIcon class="w-4 h-4" /> Bus 1
                    </button>
                    <button @click="tabActiva = 'bus2'"
                        :class="tabActiva === 'bus2' ? 'bg-white text-amber-600 shadow-md' : 'text-slate-500'"
                        class="flex-1 flex items-center justify-center gap-2 py-2.5 rounded-xl font-bold transition-all duration-200 text-sm">
                        <TruckIcon class="w-4 h-4" /> Bus 2
                    </button>
                </nav>
            </div>

            <!-- Buscador Fijo (Solo en Listas) -->
            <div v-if="tabActiva !== 'registro'" class="px-4 pb-3 max-w-md mx-auto animate-in fade-in duration-300">
                <div class="relative group">
                    <MagnifyingGlassIcon class="absolute left-4 top-1/2 -translate-y-1/2 w-5 h-5 text-slate-400" />
                    <input v-model="filtroNombre" type="text" :placeholder="'Buscar en Bus ' + tabActiva.at(-1)"
                        class="w-full pl-12 pr-4 py-3 rounded-2xl border-none bg-slate-100 outline-none font-bold text-slate-700 focus:ring-2 focus:ring-orange-500/20" />
                </div>
            </div>
        </div>

        <main class="mx-auto max-w-md p-4 pb-24">

            <!-- CONTENIDO DINÁMICO CON TRANSICIONES -->
            <Transition name="fade" mode="out-in">
                <!-- VISTA: REGISTRO -->
                <section v-if="tabActiva === 'registro'" key="registro" class="space-y-4">
                    <div class="rounded-3xl bg-white p-6 shadow-xl shadow-slate-200/50 border border-slate-100">
                        <div class="mb-6 flex items-center gap-3">
                            <div :class="editandoPasajeroId ? 'bg-amber-100 text-amber-600' : 'bg-orange-100 text-orange-600'"
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
                                <label class="text-xs font-black uppercase text-slate-400 ml-1">Nombre
                                    Completo</label>
                                <input v-model="formulario.nombre" @input="validarNombre" type="text" required
                                    placeholder="Ej. JUAN PEREZ"
                                    class="w-full rounded-2xl border-slate-200 bg-slate-50 px-5 py-4 text-base font-bold outline-none transition focus:ring-4 focus:ring-orange-500/10 focus:bg-white uppercase" />
                            </div>

                            <div class="grid grid-cols-2 gap-4">
                                <div class="space-y-1.5">
                                    <label class="text-xs font-black uppercase text-slate-400 ml-1">Teléfono</label>
                                    <input v-model="formulario.telefono" @input="validarTelefono" type="tel"
                                        inputmode="numeric" placeholder="Ej. 987654321"
                                        class="w-full rounded-2xl border-slate-200 bg-slate-50 px-5 py-4 text-base font-bold outline-none transition focus:bg-white" />
                                </div>
                                <div class="space-y-1.5">
                                    <label
                                        class="text-xs font-black uppercase text-slate-400 ml-1 text-center block">Unidad</label>
                                    <div class="grid grid-cols-2 gap-2 bg-slate-100 p-1.5 rounded-2xl h-[60px]">
                                        <button type="button" @click="formulario.bus_asignado = 1"
                                            :class="formulario.bus_asignado === 1 ? 'bg-white text-orange-600 shadow-sm' : 'text-slate-500'"
                                            class="rounded-xl text-sm font-black transition-all">Bus 1</button>
                                        <button type="button" @click="formulario.bus_asignado = 2"
                                            :class="formulario.bus_asignado === 2 ? 'bg-white text-amber-600 shadow-sm' : 'text-slate-500'"
                                            class="rounded-xl text-sm font-black transition-all">Bus 2</button>
                                    </div>
                                </div>
                            </div>

                            <div class="space-y-1.5">
                                <div class="flex justify-between items-center ml-1">
                                    <label class="text-xs font-black uppercase text-slate-400">Nota opcional</label>
                                    <span class="text-xs font-bold"
                                        :class="(formulario.nota || '').length >= 150 ? 'text-red-500' : 'text-slate-400'">
                                        {{ (formulario.nota || '').length }}/150
                                    </span>
                                </div>
                                <textarea v-model="formulario.nota" rows="3" maxlength="150"
                                    placeholder="Ejemplo: Subirá en el camino."
                                    class="w-full rounded-2xl border-slate-200 bg-slate-50 px-5 py-3 text-sm font-medium outline-none transition focus:bg-white resize-none"></textarea>
                            </div>

                            <div class="flex flex-col gap-3 pt-4">
                                <button type="submit" :disabled="guardando || !formulario.nombre.trim()"
                                    :class="editandoPasajeroId ? 'bg-amber-500 shadow-amber-200' : 'bg-orange-600 shadow-orange-200'"
                                    class="w-full rounded-2xl py-5 font-black text-lg text-white shadow-lg transition active:scale-95 disabled:opacity-50 flex items-center justify-center gap-2 uppercase tracking-wide">
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
                    <!-- Lista Estilo Card -->
                    <div class="space-y-3 px-1">
                        <TransitionGroup name="list">
                            <div v-for="(p, index) in pasajerosFiltrados" :key="p.id"
                                class="bg-white p-2.5 rounded-2xl shadow-sm border border-slate-100 flex items-center gap-3 active:scale-[0.98] transition-transform">
                                <div :class="tabActiva === 'bus1' ? 'bg-orange-50 text-orange-600' : 'bg-amber-50 text-amber-600'"
                                    class="w-8 h-8 rounded-lg flex items-center justify-center flex-shrink-0 font-bold text-xs border border-current/10">
                                    {{ index + 1 }}
                                </div>
                                <div class="flex-1 min-w-0">
                                    <h4
                                        class="font-bold text-slate-800 text-sm leading-tight break-words tracking-tight uppercase">
                                        {{ p.nombre_completo }}
                                    </h4>
                                    <p v-if="p.telefono"
                                        class="text-[12px] font-semibold text-slate-500 mt-0.5 flex items-center gap-1">
                                        <PhoneIcon class="w-3 h-3" /> {{ p.telefono }}
                                    </p>
                                </div>
                                <div class="flex items-center gap-1">
                                    <button v-if="p.telefono" @click="pasajeroALlamar = p"
                                        class="p-2 text-green-600 bg-green-50 rounded-lg active:scale-90 transition-transform"
                                        title="Llamar">
                                        <PhoneIcon class="w-4 h-4" />
                                    </button>
                                    <button v-if="p.nota" @click="notaEnModal = p"
                                        class="p-2 text-blue-600 bg-blue-50 rounded-lg active:scale-90 transition-transform"
                                        title="Ver nota">
                                        <DocumentTextIcon class="w-4 h-4" />
                                    </button>
                                    <button @click="editarPasajero(p)"
                                        class="p-2 text-orange-600 bg-orange-50 rounded-lg">
                                        <PencilSquareIcon class="w-4 h-4" />
                                    </button>
                                    <button @click="eliminarPasajero(p)" class="p-2 text-red-500 bg-red-50 rounded-lg">
                                        <TrashIcon class="w-4 h-4" />
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

        <!-- Modal para Ver Nota -->
        <Transition name="fade">
            <div v-if="notaEnModal"
                class="fixed inset-0 z-[100] flex items-center justify-center p-4 bg-slate-900/60 backdrop-blur-sm"
                @click.self="notaEnModal = null">
                <div
                    class="bg-white rounded-3xl p-6 w-full max-w-xs shadow-2xl animate-in zoom-in-95 duration-200 text-center">
                    <div
                        class="w-16 h-16 bg-blue-100 text-blue-600 rounded-full flex items-center justify-center mx-auto mb-4">
                        <DocumentTextIcon class="w-8 h-8" />
                    </div>
                    <h3 class="font-black text-slate-900 uppercase text-[10px] tracking-widest mb-1 opacity-50">Nota del
                        Pasajero</h3>
                    <p class="text-sm font-bold text-slate-800 mb-4 uppercase leading-tight">
                        {{ notaEnModal.nombre_completo }}
                    </p>
                    <div
                        class="bg-slate-50 p-4 rounded-2xl border border-slate-100 text-slate-700 text-sm leading-relaxed italic whitespace-pre-wrap text-left">
                        "{{ notaEnModal.nota }}"
                    </div>
                    <button @click="notaEnModal = null"
                        class="w-full mt-6 bg-slate-900 text-white font-bold py-3.5 rounded-2xl active:scale-95 transition-transform text-xs uppercase tracking-widest">
                        Cerrar
                    </button>
                </div>
            </div>
        </Transition>

        <!-- Modal Confirmación de Llamada -->
        <Transition name="fade">
            <div v-if="pasajeroALlamar"
                class="fixed inset-0 z-[100] flex items-center justify-center p-4 bg-slate-900/60 backdrop-blur-sm"
                @click.self="pasajeroALlamar = null">
                <div
                    class="bg-white rounded-3xl p-6 w-full max-w-xs shadow-2xl animate-in zoom-in-95 duration-200 text-center">
                    <div
                        class="w-16 h-16 bg-green-100 text-green-600 rounded-full flex items-center justify-center mx-auto mb-4">
                        <PhoneIcon class="w-8 h-8" />
                    </div>
                    <h3 class="font-black text-slate-900 uppercase text-sm tracking-widest mb-2">Confirmar Llamada</h3>
                    <p class="text-slate-500 text-xs mb-6">
                        ¿Deseas llamar a <span class="font-bold text-slate-800">{{ pasajeroALlamar.nombre_completo
                            }}</span>?
                        <br>
                        <span class="text-[14px] font-black text-green-600 block mt-2">{{ pasajeroALlamar.telefono
                            }}</span>
                    </p>
                    <div class="flex gap-3">
                        <button @click="pasajeroALlamar = null"
                            class="flex-1 bg-slate-100 text-slate-500 font-bold py-3.5 rounded-2xl active:scale-95 transition-transform text-xs uppercase tracking-widest">
                            Cancelar
                        </button>
                        <a :href="'tel:' + pasajeroALlamar.telefono" @click="pasajeroALlamar = null"
                            class="flex-1 bg-green-600 text-white font-bold py-3.5 rounded-2xl active:scale-95 transition-transform text-xs uppercase tracking-widest flex items-center justify-center gap-2">
                            Llamar
                        </a>
                    </div>
                </div>
            </div>
        </Transition>

        <!-- Modal Confirmación de Eliminación -->
        <Transition name="fade">
            <div v-if="pasajeroAEliminar"
                class="fixed inset-0 z-[100] flex items-center justify-center p-4 bg-slate-900/60 backdrop-blur-sm"
                @click.self="pasajeroAEliminar = null">
                <div
                    class="bg-white rounded-3xl p-6 w-full max-w-xs shadow-2xl animate-in zoom-in-95 duration-200 text-center">
                    <div
                        class="w-16 h-16 bg-red-100 text-red-600 rounded-full flex items-center justify-center mx-auto mb-4">
                        <TrashIcon class="w-8 h-8" />
                    </div>
                    <h3 class="font-black text-slate-900 uppercase text-sm tracking-widest mb-2">Eliminar Registro</h3>
                    <p class="text-slate-500 text-xs mb-6 px-2">
                        ¿Estás seguro de eliminar a <span class="font-bold text-slate-800">{{
                            pasajeroAEliminar.nombre_completo }}</span>?
                        Esta acción no se puede deshacer.
                    </p>
                    <div class="flex gap-3">
                        <button @click="pasajeroAEliminar = null"
                            class="flex-1 bg-slate-100 text-slate-500 font-bold py-3.5 rounded-2xl active:scale-95 transition-transform text-xs uppercase tracking-widest">
                            Volver
                        </button>
                        <button @click="confirmarEliminacionDefinitiva"
                            class="flex-1 bg-red-600 text-white font-bold py-3.5 rounded-2xl active:scale-95 transition-transform text-xs uppercase tracking-widest">
                            Eliminar
                        </button>
                    </div>
                </div>
            </div>
        </Transition>
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
    MagnifyingGlassIcon,
    XMarkIcon
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
const notaEnModal = ref(null) // Controla qué nota se muestra en el modal
const pasajeroALlamar = ref(null) // Controla el modal de llamada
const pasajeroAEliminar = ref(null) // Controla el modal de eliminación
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

// Validadores en tiempo real
const validarNombre = () => {
    // Remueve números y convierte a mayúsculas
    formulario.value.nombre = formulario.value.nombre
        .replace(/[0-9]/g, '')
        .toUpperCase()
}

const validarTelefono = () => {
    // Remueve todo lo que no sea número
    formulario.value.telefono = formulario.value.telefono.replace(/\D/g, '')
}

// Guardar (insertar o actualizar) un pasajero
const guardarPasajero = async () => {
    if (guardando.value) return
    guardando.value = true

    // Guardar el bus de destino para redireccionar después
    const busDestino = formulario.value.bus_asignado

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

    // Si estábamos editando, regresamos a la lista del bus correspondiente
    if (editandoPasajeroId.value) {
        tabActiva.value = 'bus' + busDestino
    }
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

// Iniciar flujo de eliminación
const eliminarPasajero = (pasajero) => {
    pasajeroAEliminar.value = pasajero
}

const confirmarEliminacionDefinitiva = async () => {
    const id = pasajeroAEliminar.value.id
    const { error } = await supabase
        .from('pasajeros')
        .delete()
        .eq('id', id)

    if (error) {
        showNotification(`Error al eliminar pasajero: ${error.message}`, 'alert-error')
        return
    }

    pasajeroAEliminar.value = null
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