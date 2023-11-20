# Calculadora-de-filtros-graficos
Es un programa creado para calcular datos de filtros pasivos que  manualmente son tediosos y aquí buscamos simplificar y evitar el error 
import tkinter as tk
from tkinter import ttk, messagebox
import cmath

class CalculadoraFiltros(tk.Tk):
    def __init__(self):
        super().__init__()
        self.title("Calculadora de Filtros Pasivos")
        self.geometry("400x400")

        self.create_widgets()

    def create_widgets(self):
        # Filtro pasivo
        filtro_frame = ttk.LabelFrame(self, text="Filtro Pasivo")
        filtro_frame.grid(row=0, column=0, padx=10, pady=10, sticky="nsew")

        ttk.Label(filtro_frame, text="Resistencia (R):").grid(row=0, column=0)
        self.entry_R = ttk.Entry(filtro_frame)
        self.entry_R.grid(row=0, column=1)
        self.entry_R.insert(0, "1000")

        ttk.Label(filtro_frame, text="Inductancia (L):").grid(row=1, column=0)
        self.entry_L = ttk.Entry(filtro_frame)
        self.entry_L.grid(row=1, column=1)
        self.entry_L.insert(0, "0.001")

        ttk.Label(filtro_frame, text="Frecuencia de corte (Hz):").grid(row=2, column=0)
        self.entry_frecuencia_corte = ttk.Entry(filtro_frame)
        self.entry_frecuencia_corte.grid(row=2, column=1)
        self.entry_frecuencia_corte.insert(0, "2000")

        ttk.Label(filtro_frame, text="Tipo de filtro:").grid(row=3, column=0)
        self.combo_tipo_filtro = ttk.Combobox(filtro_frame, values=["Pasa Bajos", "Pasa Altos", "Pasa Banda", "Elimina Banda"])
        self.combo_tipo_filtro.grid(row=3, column=1)
        self.combo_tipo_filtro.set("Pasa Bajos")

        ttk.Button(filtro_frame, text="Calcular", command=self.calcular_respuesta_filtro).grid(row=4, column=0, columnspan=2)

        # Simplificación de circuitos
        circuito_frame = ttk.LabelFrame(self, text="Simplificación de Circuitos")
        circuito_frame.grid(row=1, column=0, padx=10, pady=10, sticky="nsew")

        ttk.Label(circuito_frame, text="Número de impedancias:").grid(row=0, column=0)
        self.entry_num_impedancias = ttk.Entry(circuito_frame)
        self.entry_num_impedancias.grid(row=0, column=1)

        ttk.Label(circuito_frame, text="Tipo de circuito:").grid(row=1, column=0)
        self.combo_tipo_circuito = ttk.Combobox(circuito_frame, values=["Serie", "Paralelo"])
        self.combo_tipo_circuito.grid(row=1, column=1)

        # Entradas para valores de impedancia
        ttk.Label(circuito_frame, text="Valores de impedancia (separados por comas):").grid(row=2, column=0, columnspan=2)
        self.entry_impedancias = ttk.Entry(circuito_frame)
        self.entry_impedancias.grid(row=3, column=0, columnspan=2)

        ttk.Button(circuito_frame, text="Simplificar", command=self.simplificar_circuitos).grid(row=4, column=0, columnspan=2)

        #  resultados de circuitos en serie/paralelo
        resultados_frame_circuitos = ttk.LabelFrame(self, text="Resultados Circuitos")
        resultados_frame_circuitos.grid(row=2, column=0, padx=10, pady=10, sticky="nsew")

        self.tabla_resultados_circuitos = ttk.Treeview(resultados_frame_circuitos, columns=("Impedancia", "Ángulo"))
        self.tabla_resultados_circuitos.heading("#0", text="Impedancia")
        self.tabla_resultados_circuitos.heading("#1", text="Ángulo")
        self.tabla_resultados_circuitos.grid(row=0, column=0)

        # Resultados
        resultados_frame = ttk.LabelFrame(self, text="Resultados")
        resultados_frame.grid(row=3, column=0, padx=10, pady=10, sticky="nsew")

        self.resultados_text = tk.Text(resultados_frame, height=8, width=40)
        self.resultados_text.grid(row=0, column=0)

    def calcular_respuesta_filtro(self):
        try:
            R = float(self.entry_R.get())
            L = float(self.entry_L.get())
            frecuencia_corte = float(self.entry_frecuencia_corte.get())
            tipo_filtro = self.combo_tipo_filtro.get()
        except ValueError:
            self.mostrar_error("Ingrese valores numéricos válidos.")
            return

        if tipo_filtro == "Pasa Bajos":
            magnitud, fase = self.calcular_respuesta_pasa_bajos(R, L, frecuencia_corte)
        elif tipo_filtro == "Pasa Altos":
            magnitud, fase = self.calcular_respuesta_pasa_altos(R, L, frecuencia_corte)
        elif tipo_filtro == "Pasa Banda":
            #  implementar función para filtro pasa banda
            magnitud, fase = 0, 0
        elif tipo_filtro == "Elimina Banda":
            #  implementar  función para  filtro elimina banda
            magnitud, fase = 0, 0
        else:
            self.mostrar_error("Seleccione un tipo de filtro válido.")
            return

        resultados = f"\nRespuesta del filtro {tipo_filtro}:\nMagnitud: {magnitud}\nFase: {fase} grados"

        self.resultados_text.delete("1.0", tk.END)
        self.resultados_text.insert(tk.END, resultados)

    def calcular_respuesta_pasa_bajos(self, R, L, frecuencia_corte):
        omega_corte = 2 * cmath.pi * frecuencia_corte
        Z_L = 1j * omega_corte * L
        Z_C = 1 / (1j * omega_corte)
        Z_total = R + Z_L + Z_C

        magnitud_total = abs(Z_total)
        fase_total = cmath.phase(Z_total) * (180.0 / cmath.pi)

        return magnitud_total, fase_total

    def calcular_respuesta_pasa_altos(self, R, L, frecuencia_corte):
        omega_corte = 2 * cmath.pi * frecuencia_corte
        Z_L = 1j * omega_corte * L
        Z_C = 1 / (1j * omega_corte)
        Z_total = R + Z_L + Z_C

        magnitud_total = abs(Z_total)
        fase_total = cmath.phase(Z_total) * (180.0 / cmath.pi)

        return magnitud_total, fase_total

    def simplificar_circuitos(self):
        try:
            num_impedancias = int(self.entry_num_impedancias.get())
            tipo = self.combo_tipo_circuito.get()
            impedancias_str = self.entry_impedancias.get().replace(" ", "")
            impedancias = [complex(z) for z in impedancias_str.split(",")]
        except ValueError:
            self.mostrar_error("Ingrese valores numéricos válidos para el número de impedancias.")
            return

        if tipo == "Serie":
            magnitud_total, fase_total = self.simplificar_circuitos_serie(impedancias)
        elif tipo == "Paralelo":
            magnitud_total, fase_total = self.simplificar_circuitos_paralelo(impedancias)
        else:
            self.mostrar_error("Seleccione un tipo de circuito válido.")
            return

        # Limpiar tabla de resultados
        for item in self.tabla_resultados_circuitos.get_children():
            self.tabla_resultados_circuitos.delete(item)

        # Llenar tabla con resultados
        for i, (z, angle) in enumerate(zip(magnitud_total, fase_total), 1):
            self.tabla_resultados_circuitos.insert("", "end", text=f"Impedancia {i}", values=(f"{z:.2f}", f"{angle:.2f}"))

    def simplificar_circuitos_serie(self, impedancias):
        Z_total = sum(impedancias)
        magnitud_total = [abs(Z_total)] * len(impedancias)
        fase_total = [cmath.phase(Z_total)] * len(impedancias)
        return magnitud_total, fase_total

    def simplificar_circuitos_paralelo(self, impedancias):
        Z_total_inv = sum(1/z for z in impedancias)
        Z_total = 1 / Z_total_inv
        magnitud_total = [abs(Z_total)] * len(impedancias)
        fase_total = [cmath.phase(Z_total)] * len(impedancias)
        return magnitud_total, fase_total

    def mostrar_error(self, mensaje):
        messagebox.showerror("Error", mensaje)

if __name__ == "__main__":
    app = CalculadoraFiltros()
    app.mainloop()
