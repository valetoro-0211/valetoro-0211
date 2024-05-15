#include <iostream>
#include <fstream>
#include <vector>
#include <cmath>

using namespace std;

// Función para leer números desde un archivo
vector<vector<double>> readNumbersFromFile(const string& filename) {
    ifstream file(filename);
    int numElements, numVectors;
    file >> numElements >> numVectors;
    vector<vector<double>> matrix(numVectors, vector<double>(numElements));
    for (int i = 0; i < numVectors; i++) {
        for (int j = 0; j < numElements; j++) {
            file >> matrix[i][j];
        }
    }
    file.close();
    return matrix;
}

// Función para convertir una matriz a una base ortogonal
vector<vector<double>> toBaseOrthogonal(const vector<vector<double>>& matrix) {
    int numRows = matrix.size();
    int numCols = matrix[0].size();
    vector<vector<double>> Q(numRows, vector<double>(numCols));
    for (int i = 0; i < numRows; i++) {
        for (int j = 0; j < numCols; j++) {
            Q[i][j] = matrix[i][j];
        }
    }
    // Aplicar el algoritmo de Gram-Schmidt
    for (int i = 0; i < numRows; i++) {
        for (int j = 0; j < i; j++) {
            double factor = 0;
            for (int k = 0; k < numCols; k++) {
                factor += Q[j][k] * Q[i][k];
            }
            factor = -factor / (Q[j][j] * Q[j][j]);
            for (int k = 0; k < numCols; k++) {
                Q[i][k] += factor * Q[j][k];
            }
        }
        double norm = 0;
        for (int j = 0; j < numCols; j++) {
            norm += Q[i][j] * Q[i][j];
        }
        norm = sqrt(norm);
        for (int j = 0; j < numCols; j++) {
            Q[i][j] /= norm;
        }
    }
    return Q;
}

// Función para normalizar una matriz
vector<vector<double>> normalize(const vector<vector<double>>& matrix) {
    int numRows = matrix.size();
    int numCols = matrix[0].size();
    vector<vector<double>> normalized(numRows, vector<double>(numCols));
    for (int i = 0; i < numRows; i++) {
        double norm = 0;
        for (int j = 0; j < numCols; j++) {
            norm += matrix[i][j] * matrix[i][j];
        }
        norm = sqrt(norm);
        for (int j = 0; j < numCols; j++) {
            normalized[i][j] = matrix[i][j] / norm;
        }
    }
    return normalized;
}

int main() {
    // Leer la matriz desde el archivo
    vector<vector<double>> matrix = readNumbersFromFile("numbers.txt");
    // Convertir la matriz a una base ortogonal
    vector<vector<double>> Q = toBaseOrthogonal(matrix);
    // Normalizar la matriz
    vector<vector<double>> normalized = normalize(Q);
    // Imprimir la matriz normalizada
    for (int i = 0; i < normalized.size(); i++) {
        for (int j = 0; j < normalized[i].size(); j++) {
            cout << normalized[i][j] << " ";
        }
        cout << endl;
    }
    return 0;
}
