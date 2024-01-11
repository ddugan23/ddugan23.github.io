---
title: "HEIC to JPEG Converter in C"
excerpt: "I was tired of having to use an online converter to upload iPhone pictures on websites that did not take .HEIC files. So I made a converter myself!<br/><img src='/images/converter.png'>"
collection: projects
---


## Introduction
This post delves into the development of a C program I created for converting .HEIC images to JPEG format, featuring a graphical user interface (GUI). Through this project, I was able to develop a better understanding of image formats, memory management, and intuitive interface design.

## Problem Definition
The task was to develop a program that:
- Efficiently converts .HEIC images to JPEG.
- Utilizes external libraries (`libheif` and `libjpeg`) for image processing.
- Provides a simple and user-friendly GUI for interaction.

## Development Process

### Reading .HEIC Files
The first step in the conversion process was to read the .HEIC files. This was achieved using the `libheif` library.

```cpp
struct heif_context* ctx = heif_context_alloc();
struct heif_error error = heif_context_read_from_file(ctx, input_path, NULL);

if (error.code != heif_error_Ok) {
    fprintf(stderr, "Error reading HEIC file: %s\n", error.message);
    exit(1);
}
```

### Converting to JPEG
After successfully reading the .HEIC file, the next step was converting it to the JPEG format using `libjpeg`, a widely used library for JPEG operations.

```cpp
struct jpeg_compress_struct cinfo;
struct jpeg_error_mgr jerr;
cinfo.err = jpeg_std_error(&jerr);
jpeg_create_compress(&cinfo);
jpeg_stdio_dest(&cinfo, outfile);

cinfo.image_width = heif_image_handle_get_width(handle);
cinfo.image_height = heif_image_handle_get_height(handle);
cinfo.input_components = 3;
cinfo.in_color_space = JCS_RGB;

jpeg_set_defaults(&cinfo);
jpeg_set_quality(&cinfo, 90, TRUE);
jpeg_start_compress(&cinfo, TRUE);
// Write scanlines...
jpeg_finish_compress(&cinfo);
jpeg_destroy_compress(&cinfo);
```

### GUI Implementation
The GUI, created using GTK+, is a key component of this project. It offers a more interactice and user-friendly way for users to select files and initiate the conversion process.

```Cpp
// Initialize GTK+
gtk_init(&argc, &argv);

// Create main window and set properties
window = gtk_window_new(GTK_WINDOW_TOPLEVEL);
gtk_window_set_title(GTK_WINDOW(window), "HEIC to JPEG Converter");
gtk_window_set_default_size(GTK_WINDOW(window), 400, 200);
g_signal_connect(window, "destroy", G_CALLBACK(gtk_main_quit), NULL);

// Create and pack widgets into the window
// ...

gtk_widget_show_all(window);
gtk_main();
```

### Handling User Input
The program handles input through callbacks, ensuring a responsive and interactive experience. File choosers are used for selecting the .HEIC file and the output directory, while an entry field allows users to specify the output filename.

```Cpp
void on_input_file_set(GtkFileChooserButton *button, gpointer user_data) {
    gchar *filename = gtk_file_chooser_get_filename(GTK_FILE_CHOOSER(button));
    strncpy(input_path, filename, sizeof(input_path));
    g_free(filename);
}

void on_output_folder_set(GtkFileChooserButton *button, gpointer user_data) {
    gchar *foldername = gtk_file_chooser_get_filename(GTK_FILE_CHOOSER(button));
    strncpy(output_folder_path, foldername, sizeof(output_folder_path));
    g_free(foldername);
}
```

### Callback for Convert Button Click
The `on_convert_clicked` function is triggered when the user clicks the "Convert" button. It handles the conversion process and updates the button label upon completion.

```Cpp
void on_convert_clicked(GtkButton *button, gpointer user_data) {
    GtkWidget *entry = GTK_WIDGET(user_data);
    const char *filename = gtk_entry_get_text(GTK_ENTRY(entry));

    // Ensure valid input and output paths
    char output_file_path[1280];
    snprintf(output_file_path, sizeof(output_file_path), "%s/%s", output_folder_path, filename);

    convert_heic_to_jpeg(input_path, output_file_path);
    gtk_button_set_label(button, "Conversion complete!");
}
```

### Compilation and Execution
The program is compiled using GCC, linking against GTK+, `libheif`, and `libjpeg`. The compilation command includes the necessary flags for these libraries:
```bash
gcc `pkg-config --cflags gtk+-3.0` -o heic_to_jpeg conversion.c `pkg-config --libs gtk+-3.0 libheif libjpeg`
```


### Full Program
```Cpp
#include <gtk/gtk.h>
#include <jpeglib.h>
#include <libheif/heif.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// Function Declarations
void convert_heic_to_jpeg(const char* input_path, const char* output_path);
void on_input_file_set(GtkFileChooserButton *button, gpointer user_data);
void on_output_folder_set(GtkFileChooserButton *button, gpointer user_data);
void on_convert_clicked(GtkButton *button, gpointer user_data);

// Global Variables for File Paths
char input_path[1024] = {0};
char output_folder_path[1024] = {0};
char output_filename[256] = {0};

// Main Function
int main(int argc, char *argv[]) {
    GtkWidget *window;
    GtkWidget *convert_button;
    GtkWidget *input_button, *input_label;
    GtkWidget *output_folder_button, *output_folder_label;
    GtkWidget *output_filename_entry, *output_filename_label;
    GtkWidget *vbox;

    // Initialize GTK+
    gtk_init(&argc, &argv);

    // Create the main window
    window = gtk_window_new(GTK_WINDOW_TOPLEVEL);
    gtk_window_set_title(GTK_WINDOW(window), "HEIC to JPEG Converter");
    gtk_window_set_default_size(GTK_WINDOW(window), 400, 200);
    g_signal_connect(window, "destroy", G_CALLBACK(gtk_main_quit), NULL);

    // Create Vertical Box
    vbox = gtk_box_new(GTK_ORIENTATION_VERTICAL, 5);
    gtk_container_add(GTK_CONTAINER(window), vbox);

    // Input File Chooser
    input_label = gtk_label_new("Select HEIC File:");
    gtk_box_pack_start(GTK_BOX(vbox), input_label, FALSE, FALSE, 0);
    input_button = gtk_file_chooser_button_new("Select HEIC File", GTK_FILE_CHOOSER_ACTION_OPEN);
    g_signal_connect(input_button, "file-set", G_CALLBACK(on_input_file_set), NULL);
    gtk_box_pack_start(GTK_BOX(vbox), input_button, FALSE, FALSE, 0);

    // Output Folder Chooser
    output_folder_label = gtk_label_new("Select Output Folder:");
    gtk_box_pack_start(GTK_BOX(vbox), output_folder_label, FALSE, FALSE, 0);
    output_folder_button = gtk_file_chooser_button_new("Select Output Folder", GTK_FILE_CHOOSER_ACTION_SELECT_FOLDER);
    g_signal_connect(output_folder_button, "file-set", G_CALLBACK(on_output_folder_set), NULL);
    gtk_box_pack_start(GTK_BOX(vbox), output_folder_button, FALSE, FALSE, 0);

    // Output Filename Entry
    output_filename_label = gtk_label_new("Output File Name:");
    gtk_box_pack_start(GTK_BOX(vbox), output_filename_label, FALSE, FALSE, 0);
    output_filename_entry = gtk_entry_new();
    gtk_entry_set_text(GTK_ENTRY(output_filename_entry), "output.jpg");  // Default filename
    gtk_box_pack_start(GTK_BOX(vbox), output_filename_entry, FALSE, FALSE, 0);

    // Convert Button
    convert_button = gtk_button_new_with_label("Convert");
    g_signal_connect(convert_button, "clicked", G_CALLBACK(on_convert_clicked), output_filename_entry);
    gtk_box_pack_start(GTK_BOX(vbox), convert_button, FALSE, FALSE, 0);

    // Show all widgets
    gtk_widget_show_all(window);

    // Run the GTK+ main loop
    gtk_main();

    return 0;
}

// Callback for Input File Selection
void on_input_file_set(GtkFileChooserButton *button, gpointer user_data) {
    gchar *filename = gtk_file_chooser_get_filename(GTK_FILE_CHOOSER(button));
    strncpy(input_path, filename, sizeof(input_path));
    g_free(filename);
}

// Callback for Output Folder Selection
void on_output_folder_set(GtkFileChooserButton *button, gpointer user_data) {
    gchar *foldername = gtk_file_chooser_get_filename(GTK_FILE_CHOOSER(button));
    strncpy(output_folder_path, foldername, sizeof(output_folder_path));
    g_free(foldername);
}

// Callback for Convert Button Click
void on_convert_clicked(GtkButton *button, gpointer user_data) {
    GtkWidget *entry = GTK_WIDGET(user_data);
    const char *filename = gtk_entry_get_text(GTK_ENTRY(entry));

    if (input_path[0] == '\0' || output_folder_path[0] == '\0' || filename[0] == '\0') {
        g_print("Please select an input file, output folder, and specify an output file name.\n");
        return;
    }

    char output_file_path[1280];
    snprintf(output_file_path, sizeof(output_file_path), "%s/%s", output_folder_path, filename);

    convert_heic_to_jpeg(input_path, output_file_path);
    gtk_button_set_label(button, "Conversion complete!");
}

void convert_heic_to_jpeg(const char* input_path, const char* output_path) {
    // Read the .HEIC file
    struct heif_context* ctx = heif_context_alloc();
    struct heif_error error = heif_context_read_from_file(ctx, input_path, NULL);

    if (error.code != heif_error_Ok) {
        fprintf(stderr, "Error reading HEIC file: %s\\n", error.message);
        exit(1);
    }

    // Get the primary image handle
    struct heif_image_handle* handle;
    error = heif_context_get_primary_image_handle(ctx, &handle);

    if (error.code != heif_error_Ok) {
        fprintf(stderr, "Error getting primary image handle: %s\\n", error.message);
        exit(1);
    }

    // Decode the image
    struct heif_image* img;
    error = heif_decode_image(handle, &img, heif_colorspace_RGB, heif_chroma_interleaved_RGB, NULL);

    if (error.code != heif_error_Ok) {
        fprintf(stderr, "Error decoding image: %s\\n", error.message);
        exit(1);
    }

    // Get image data
    int stride;
    const uint8_t* data = heif_image_get_plane_readonly(img, heif_channel_interleaved, &stride);

    // Open output JPEG file
    FILE* outfile = fopen(output_path, "wb");
    if (outfile == NULL) {
        fprintf(stderr, "Error opening JPEG file for writing.\\n");
        exit(1);
    }

    // JPEG compression settings
    struct jpeg_compress_struct cinfo;
    struct jpeg_error_mgr jerr;
    cinfo.err = jpeg_std_error(&jerr);
    jpeg_create_compress(&cinfo);
    jpeg_stdio_dest(&cinfo, outfile);

    cinfo.image_width = heif_image_handle_get_width(handle);
    cinfo.image_height = heif_image_handle_get_height(handle);
    cinfo.input_components = 3; // RGB
    cinfo.in_color_space = JCS_RGB;

    jpeg_set_defaults(&cinfo);
    jpeg_set_quality(&cinfo, 90, TRUE); // Quality: 90

    // Start compression
    jpeg_start_compress(&cinfo, TRUE);
    while (cinfo.next_scanline < cinfo.image_height) {
        const uint8_t* row_pointer = data + cinfo.next_scanline * stride;
        jpeg_write_scanlines(&cinfo, (JSAMPARRAY)&row_pointer, 1);
    }

    // Finish compression
    jpeg_finish_compress(&cinfo);
    jpeg_destroy_compress(&cinfo);

    // Close files and clean up
    fclose(outfile);
    heif_image_release(img);
    heif_image_handle_release(handle);
    heif_context_free(ctx);
}
```

### Conclusion
Developing this HEIC to JPEG converter was a rewarding experience because it was the first time I was able to use my programming experience to create a practical application to solve a real problem of mine. No longer will I need to upload pictures of my license or passport to random websites just to convert them to an acceptable format!