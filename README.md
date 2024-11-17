#include <iostream>
using namespace std;

#define MAX 10

int* match(int n, int** score);

int main()
{
    int* sense[MAX];
    int n;
    cin >> n;

    for (int i = 0; i < n; i++)
    {
        sense[i] = new int[n];
        for (int j = 0; j < n; j++)
        {
            cin >> sense[i][j];
        }
    }

    int* pair = match(n, sense);

    for (int i = 0; i < n; i++)
    {
        cout << "boy " << pair[2 * i] << " pair with girl " << pair[2 * i + 1] << "\n";
    }

    delete[] pair;
    pair = nullptr;

    for (int i = 0; i < n; i++)
    {
        delete[] sense[i];
    }

    return 0;
}

// Function to match boys and girls based on preferences
int* match(int n, int** score)

{
    int* matchGirl= new int[n];

    int** sortScore_boys = new int*[n];
    for (int i = 0; i < n; i++)
    {
        sortScore_boys[i] = new int[n];
    }

    int* HigherscoreGirls = new int[n];

    // Copy the score matrix to the sortScore_boys matrix
    for (int i = 0; i < n; i++)
    {
        for (int j = 0; j < n; j++)
        {
            sortScore_boys[i][j] = score[i][j];
        }
    }

    // Bubble sort each row based on preferences in descending order
    for (int i = 0; i < n; i++)
    {
        for (int k = 0; k < n - 1; k++) // Bubble sort over n-1 passes
        {
            for (int j = 0; j < n - 1 - k; j++) // Compare adjacent elements
            {
                if (sortScore_boys[i][j] < sortScore_boys[i][j + 1]) // Swap for descending order
                {
                    int temp = sortScore_boys[i][j];
                    sortScore_boys[i][j] = sortScore_boys[i][j + 1];
                    sortScore_boys[i][j + 1] = temp;
                }
            }
        }
    }

    for (int j=0; j<n; j++ )
    {
        int maxValue = score[0][j];
        for (int i=1; i<n; i++)
        {
            if (score[i][j]> maxValue)
            {
                maxValue= score[i][j];
            }
        }
        HigherscoreGirls[j]= maxValue;
    }

    // Define the recursive lambda function
    auto Search_match = [&] (int n, int* maxColumn, int** sortedArr, int** originalArr) -> int*
    {
        int* matchColumn = new int[MAX];

        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (maxColumn[i] == sortedArr[j][0]) {
                    for (int k = 0; k < n; k++) {
                        for (int l = 0; l < n; l++) {
                            if (sortedArr[j][0] == originalArr[k][l]) {
                                matchColumn[i] = l + 1;
                            }
                        }
                    }
                } else {
                    // Recursive call
                    Search_match(n, maxColumn, sortedArr, originalArr);  // Recursive call
                }
            }
        }

        return matchColumn;
    };

    matchGirl = Search_match(n, HigherscoreGirls, sortScore_boys, score);

    return matchGirl;
}
