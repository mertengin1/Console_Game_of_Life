#include <iostream>
#include <cstdlib>
#include <ctime>
#include <unistd.h> // usleep() için

using namespace std;

class GameOfLife {
private:
    int grid[20][20];  // 20x20'lik hücre matrisi

public:
    // Yapıcı fonksiyon, başlangıçta rastgele hücre durumlarını belirliyoruz
    GameOfLife() {
        // Matrisin tamamını sıfırlarla dolduruyoruz
        for (int i = 0; i < 20; i++) {
            for (int j = 0; j < 20; j++) {
                grid[i][j] = rand() % 2;  // Rastgele olarak 0 veya 1 koyuyoruz
            }
        }
    }

    // Komşuları sayma fonksiyonu
    int countNeighbors(int x, int y) {
        int count = 0;
        // 8 komşuyu kontrol et
        for (int i = -1; i <= 1; i++) {
            for (int j = -1; j <= 1; j++) {
                int nx = x + i;
                int ny = y + j;
                // Komşu hücrenin geçerli olup olmadığını kontrol et
                if (nx >= 0 && nx < 20 && ny >= 0 && ny < 20 && (i != 0 || j != 0)) {
                    count += grid[nx][ny];  // Canlı komşuları say
                }
            }
        }
        return count;
    }

    // Yeni nesil oluşturma fonksiyonu
    void nextGeneration() {
        int newGrid[20][20];

        for (int i = 0; i < 20; i++) {
            for (int j = 0; j < 20; j++) {
                int neighbors = countNeighbors(i, j);

                if (grid[i][j] == 1) {
                    // Canlı hücre
                    if (neighbors < 2 || neighbors > 3) {
                        newGrid[i][j] = 0;  // Ölür
                    } else {
                        newGrid[i][j] = 1;  // Hayatta kalır
                    }
                } else {
                    // Ölü hücre
                    if (neighbors == 3) {
                        newGrid[i][j] = 1;  // Canlanır
                    } else {
                        newGrid[i][j] = 0;  // Ölü kalır
                    }
                }
            }
        }

        // Yeni nesli mevcut grid'e kopyala
        for (int i = 0; i < 20; i++) {
            for (int j = 0; j < 20; j++) {
                grid[i][j] = newGrid[i][j];
            }
        }
    }

    // Hücrelerin durumunu ekrana yazdıran fonksiyon
    void print() {
        for (int i = 0; i < 20; i++) {
            for (int j = 0; j < 20; j++) {
                cout << grid[i][j] << " ";
            }
            cout << endl;
        }
    }
};

int main() {
    GameOfLife game;

    // Başlangıç durumunu yazdır
    cout << "Başlangıç Durumu:" << endl;
    game.print();
    cout << endl;

    // Sonsuz döngü içinde oyun devam etsin
    while (true) {
        // Bir sonraki nesli oluştur
        game.nextGeneration();

        // Durumu yazdır
        game.print();
        cout << endl;

        // 0.5 saniye bekle
        usleep(500000);
    }

    return 0;
}
