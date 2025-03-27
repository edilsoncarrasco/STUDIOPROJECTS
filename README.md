import 'package:flutter/material.dart';
import 'database_helper.dart';
import 'libro.dart';

void main() {
  runApp(MaterialApp(
    theme: ThemeData(
      primarySwatch: Colors.teal,
      scaffoldBackgroundColor: Color(0xFFE0F2F1),
      inputDecorationTheme: InputDecorationTheme(
        filled: true,
        fillColor: Colors.white,
        border: OutlineInputBorder(
          borderRadius: BorderRadius.circular(4),
          borderSide: BorderSide.none,
        ),
      ),
    ),
    home: LibrosApp(),
  ));
}

class LibrosApp extends StatefulWidget {
  @override
  _LibrosAppState createState() => _LibrosAppState();
}

class _LibrosAppState extends State<LibrosApp> {
  final dbHelper = DatabaseHelper();
  List<Libro> libros = [];

  @override
  void initState() {
    super.initState();
    _cargarLibros();
  }

  Future<void> _cargarLibros() async {
    final datos = await dbHelper.getLibros();
    setState(() {
      libros = datos;
    });
  }

  Future<void> _agregarLibro(Libro libro) async {
    if (libro.titulo.isNotEmpty && libro.autor.isNotEmpty) {
      await dbHelper.insertLibro(libro);
      _cargarLibros();
    }
  }

  Future<void> _eliminarLibro(int id) async {
    await dbHelper.deleteLibro(id);
    _cargarLibros();
  }

  void _mostrarFormulario() {
    final TextEditingController tituloController = TextEditingController();
    final TextEditingController autorController = TextEditingController();
    final TextEditingController editorialController = TextEditingController();
    final TextEditingController numeroPaginasController = TextEditingController();
    final TextEditingController formatoController = TextEditingController();
    final TextEditingController idiomaController = TextEditingController();
    final TextEditingController estadoController = TextEditingController();
    final TextEditingController resenaController = TextEditingController();
    final TextEditingController pequenaResenaController = TextEditingController();
    final TextEditingController recomendacionController = TextEditingController();
    
    String anioPublicacion = DateTime.now().year.toString();
    int calificacion = 0;

    showDialog(
      context: context,
      barrierDismissible: false,
      builder: (context) {
        return StatefulBuilder(
          builder: (context, setState) {
            return Dialog(
              backgroundColor: Color(0xFFE0F2F1),
              child: Container(
                width: double.maxFinite,
                child: SingleChildScrollView(
                  padding: EdgeInsets.all(16),
                  child: Column(
                    mainAxisSize: MainAxisSize.min,
                    crossAxisAlignment: CrossAxisAlignment.start,
                    children: [
                      _buildLabeledField('Título del Libro', tituloController),
                      SizedBox(height: 12),
                      
                      _buildLabeledField('Autor', autorController),
                      SizedBox(height: 12),
                      
                      Row(
                        crossAxisAlignment: CrossAxisAlignment.start,
                        children: [
                          Expanded(
                            child: _buildLabeledField('Editorial', editorialController),
                          ),
                          SizedBox(width: 12),
                          Expanded(
                            child: Column(
                              crossAxisAlignment: CrossAxisAlignment.start,
                              children: [
                                Text('Año de Publicación'),
                                SizedBox(height: 4),
                                Container(
                                  decoration: BoxDecoration(
                                    color: Colors.white,
                                    borderRadius: BorderRadius.circular(4),
                                  ),
                                  child: DropdownButtonFormField<String>(
                                    value: anioPublicacion,
                                    decoration: InputDecoration(
                                      contentPadding: EdgeInsets.symmetric(horizontal: 12),
                                      border: InputBorder.none,
                                    ),
                                    items: List.generate(50, (index) {
                                      final year = DateTime.now().year - index;
                                      return DropdownMenuItem(
                                        value: year.toString(),
                                        child: Text(year.toString()),
                                      );
                                    }),
                                    onChanged: (value) {
                                      anioPublicacion = value!;
                                    },
                                  ),
                                ),
                              ],
                            ),
                          ),
                        ],
                      ),
                      SizedBox(height: 12),
                      
                      Row(
                        crossAxisAlignment: CrossAxisAlignment.start,
                        children: [
                          Expanded(
                            child: _buildLabeledField('Número de Páginas', numeroPaginasController),
                          ),
                          SizedBox(width: 12),
                          Expanded(
                            child: _buildLabeledField('Formato', formatoController),
                          ),
                        ],
                      ),
                      SizedBox(height: 12),
                      
                      Row(
                        crossAxisAlignment: CrossAxisAlignment.start,
                        children: [
                          Expanded(
                            child: _buildLabeledField('Idioma', idiomaController),
                          ),
                          SizedBox(width: 12),
                          Expanded(
                            child: _buildLabeledField('Estado', estadoController),
                          ),
                        ],
                      ),
                      SizedBox(height: 12),
                      
                      _buildLabeledTextArea('Reseña', resenaController, 3),
                      SizedBox(height: 12),
                      
                      _buildLabeledTextArea('Pequeña Reseña de la Trama', pequenaResenaController, 3),
                      SizedBox(height: 12),
                      
                      _buildLabeledTextArea('Why do you recommend this book?', recomendacionController, 3),
                      SizedBox(height: 12),
                      
                      Column(
                        crossAxisAlignment: CrossAxisAlignment.start,
                        children: [
                          Text('Calificación Final'),
                          SizedBox(height: 4),
                          Row(
                            mainAxisSize: MainAxisSize.min,
                            children: List.generate(5, (index) {
                              return IconButton(
                                icon: Icon(
                                  index < calificacion ? Icons.star : Icons.star_border,
                                  color: index < calificacion ? Colors.amber : Colors.grey,
                                  size: 30,
                                ),
                                onPressed: () {
                                  setState(() {
                                    calificacion = index + 1;
                                  });
                                },
                              );
                            }),
                          ),
                        ],
                      ),
                      SizedBox(height: 20),
                      
                      Center(
                        child: ElevatedButton(
                          style: ElevatedButton.styleFrom(
                            backgroundColor: Color(0xFF8E24AA),
                            foregroundColor: Colors.white,
                            padding: EdgeInsets.symmetric(horizontal: 24, vertical: 12),
                            shape: RoundedRectangleBorder(
                              borderRadius: BorderRadius.circular(20),
                            ),
                          ),
                          onPressed: () {
                            final libro = Libro(
                              titulo: tituloController.text,
                              autor: autorController.text,
                              editorial: editorialController.text,
                              anioPublicacion: anioPublicacion,
                              numeroPaginas: numeroPaginasController.text,
                              formato: formatoController.text,
                              idioma: idiomaController.text,
                              estado: estadoController.text,
                              resena: resenaController.text,
                              pequenaResena: pequenaResenaController.text,
                              recomendacion: recomendacionController.text,
                              calificacion: calificacion,
                            );
                            _agregarLibro(libro);
                            Navigator.pop(context);
                          },
                          child: Text('Enviar'),
                        ),
                      ),
                    ],
                  ),
                ),
              ),
            );
          },
        );
      },
    );
  }

  Widget _buildLabeledField(String label, TextEditingController controller) {
    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Text(label),
        SizedBox(height: 4),
        TextField(
          controller: controller,
          decoration: InputDecoration(
            contentPadding: EdgeInsets.symmetric(horizontal: 12, vertical: 12),
          ),
        ),
      ],
    );
  }

  Widget _buildLabeledTextArea(String label, TextEditingController controller, int maxLines) {
    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Text(label),
        SizedBox(height: 4),
        TextField(
          controller: controller,
          maxLines: maxLines,
          decoration: InputDecoration(
            contentPadding: EdgeInsets.symmetric(horizontal: 12, vertical: 12),
          ),
        ),
      ],
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Biblioteca de Libros'),
        backgroundColor: Color(0xFF26A69A),
      ),
      body: libros.isEmpty
          ? Center(
              child: Text(
                'No hay libros registrados.\nPresiona el botón + para agregar uno.',
                textAlign: TextAlign.center,
                style: TextStyle(fontSize: 16),
              ),
            )
          : ListView.builder(
              padding: EdgeInsets.all(8),
              itemCount: libros.length,
              itemBuilder: (context, index) {
                final libro = libros[index];
                return Card(
                  margin: EdgeInsets.only(bottom: 12),
                  elevation: 2,
                  child: Padding(
                    padding: EdgeInsets.all(12),
                    child: Column(
                      crossAxisAlignment: CrossAxisAlignment.start,
                      children: [
                        Row(
                          crossAxisAlignment: CrossAxisAlignment.start,
                          children: [
                            Expanded(
                              child: Column(
                                crossAxisAlignment: CrossAxisAlignment.start,
                                children: [
                                  Text(
                                    libro.titulo,
                                    style: TextStyle(
                                      fontSize: 18,
                                      fontWeight: FontWeight.bold,
                                    ),
                                  ),
                                  SizedBox(height: 4),
                                  Text('Autor: ${libro.autor}'),
                                  if (libro.editorial?.isNotEmpty == true)
                                    Text('Editorial: ${libro.editorial}'),
                                  if (libro.anioPublicacion?.isNotEmpty == true)
                                    Text('Año: ${libro.anioPublicacion}'),
                                ],
                              ),
                            ),
                            Row(
                              children: List.generate(5, (starIndex) {
                                return Icon(
                                  starIndex < (libro.calificacion ?? 0)
                                      ? Icons.star
                                      : Icons.star_border,
                                  color: starIndex < (libro.calificacion ?? 0)
                                      ? Colors.amber
                                      : Colors.grey,
                                  size: 20,
                                );
                              }),
                            ),
                          ],
                        ),
                        SizedBox(height: 8),
                        if (libro.pequenaResena?.isNotEmpty == true) ...[
                          Text(
                            'Reseña:',
                            style: TextStyle(fontWeight: FontWeight.bold),
                          ),
                          Text(libro.pequenaResena!),
                          SizedBox(height: 8),
                        ],
                        Row(
                          mainAxisAlignment: MainAxisAlignment.end,
                          children: [
                            TextButton(
                              onPressed: () {
                                // Ver detalles
                                showDialog(
                                  context: context,
                                  builder: (context) => AlertDialog(
                                    title: Text(libro.titulo),
                                    content: SingleChildScrollView(
                                      child: Column(
                                        crossAxisAlignment: CrossAxisAlignment.start,
                                        mainAxisSize: MainAxisSize.min,
                                        children: [
                                          Text('Autor: ${libro.autor}'),
                                          if (libro.editorial?.isNotEmpty == true)
                                            Text('Editorial: ${libro.editorial}'),
                                          if (libro.anioPublicacion?.isNotEmpty == true)
                                            Text('Año: ${libro.anioPublicacion}'),
                                          if (libro.numeroPaginas?.isNotEmpty == true)
                                            Text('Páginas: ${libro.numeroPaginas}'),
                                          if (libro.formato?.isNotEmpty == true)
                                            Text('Formato: ${libro.formato}'),
                                          if (libro.idioma?.isNotEmpty == true)
                                            Text('Idioma: ${libro.idioma}'),
                                          if (libro.estado?.isNotEmpty == true)
                                            Text('Estado: ${libro.estado}'),
                                          SizedBox(height: 8),
                                          if (libro.resena?.isNotEmpty == true) ...[
                                            Text('Reseña completa:', style: TextStyle(fontWeight: FontWeight.bold)),
                                            Text(libro.resena!),
                                            SizedBox(height: 8),
                                          ],
                                          if (libro.recomendacion?.isNotEmpty == true) ...[
                                            Text('Recomendación:', style: TextStyle(fontWeight: FontWeight.bold)),
                                            Text(libro.recomendacion!),
                                          ],
                                        ],
                                      ),
                                    ),
                                    actions: [
                                      TextButton(
                                        onPressed: () => Navigator.pop(context),
                                        child: Text('Cerrar'),
                                      ),
                                    ],
                                  ),
                                );
                              },
                              child: Text('Ver detalles'),
                            ),
                            IconButton(
                              icon: Icon(Icons.delete, color: Colors.red),
                              onPressed: () => _eliminarLibro(libro.id!),
                            ),
                          ],
                        ),
                      ],
                    ),
                  ),
                );
              },
            ),
      floatingActionButton: FloatingActionButton(
        onPressed: _mostrarFormulario,
        backgroundColor: Color(0xFF8E24AA),
        child: Icon(Icons.add),
      ),
    );
  }
}
