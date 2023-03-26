# enpruebas
texto en pruebas

---codigo Java---
package com.vista;

import com.modelo.DataHelper;
import com.modelo.Persona;
import com.db4o.ObjectContainer;
import com.db4o.ObjectSet;
import com.db4o.Db4oEmbedded;
import com.modelo.Producto;

import javax.swing.*;
import javax.swing.table.DefaultTableCellRenderer;
import javax.swing.table.TableCellRenderer;
import javax.swing.table.TableColumnModel;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.io.*;
import java.util.ArrayList;
import java.util.List;

public class VistaData {
    private JTable tablePersona;
    private JPanel PanelData;
    private JButton eliminarButton;
    private JButton modificarButton;
    private JButton añadirButton;
    private JTable tableProducto;

    public static void main(String[] args) {
        JFrame frame = new JFrame("VistaAdmin");
        frame.setContentPane(new VistaData().PanelData);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.pack();
        frame.setVisible(true);
    }

    public VistaData() {

        inicioFicheros();
        cargarDatosPersonas();
        cargarDatosProductos();

        añadirButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                
            }
        });
    }
    
    //Inicio de ficheros .yap
    public void inicioFicheros() {
        File f = new File("personas.yap");
        File f2 = new File("productos.yap");
        if(!f.exists() || !f2.exists()) {
            DataHelper.createPersona();
            DataHelper.createProducto();
        }
    }

    //Leer datos de personas
public void cargarDatosPersonas() {
        try {
            ObjectContainer db = Db4oEmbedded.openFile("personas.yap");
            List<Persona> personas = new ArrayList<>();


            Persona p = new Persona();
            ObjectSet<Persona> result = db.queryByExample(p);

            while (result.hasNext()) {
                personas.add(result.next());
            }

            db.close();

            String[] titulosPersona = {"Nombre", "Apellido", "Edad", "Direccion", "Telefono", "Email"};
            PersonaTableView personaTableView = new PersonaTableView(titulosPersona, personas);

            tablePersona.setModel(personaTableView);
            tablePersona.setVisible(true);
            adaptarColumna();//Ajusta el ancho de las columnas a los datos
            tablePersona.setDefaultRenderer(Object.class, new CenterRenderer()); // Centra los datos de la tabla

        } catch (Exception ex) {
            ex.printStackTrace();
        }
    }

    //Leer datos de productos
    public void cargarDatosProductos() {
        try {
            ObjectContainer db = Db4oEmbedded.openFile("productos.yap");
            List<Producto> productos = new ArrayList<>();


            Producto p = new Producto();
            ObjectSet<Producto> result = db.queryByExample(p);

            while (result.hasNext()) {
                productos.add(result.next());
            }

            db.close();

            String[] titulosProducto = {"Nombre", "Descripcion", "Precio", "Cantidad"};
            ProductoTableView productoTableView = new ProductoTableView(titulosProducto, productos);

            tableProducto.setModel(productoTableView);
            tableProducto.setVisible(true);
            adaptarColumna();//Ajusta el ancho de las columnas a los datos
            tableProducto.setDefaultRenderer(Object.class, new CenterRenderer()); // Centra los datos de la tabla

        } catch (Exception ex) {
            ex.printStackTrace();
        }
    }



    //Ajusta el ancho de las columnas a los datos
    public void adaptarColumna() {
        TableColumnModel columnModel = tablePersona.getColumnModel();
        for (int column = 0; column < tablePersona.getColumnCount(); column++) {
            int width = 30; // Ancho mínimo
            for (int row = 0; row < tablePersona.getRowCount(); row++) {
                TableCellRenderer renderer = tablePersona.getCellRenderer(row, column);
                Component comp = tablePersona.prepareRenderer(renderer, row, column);
                width = Math.max(comp.getPreferredSize().width +1 , width);
            }
            if(width > 900)
                width=900;
            columnModel.getColumn(column).setPreferredWidth(width);
        }
    }

    //Centra los datos de la tabla
    public class CenterRenderer extends DefaultTableCellRenderer {
        public CenterRenderer() {
            setHorizontalAlignment(SwingConstants.CENTER);
        }
    }


}

