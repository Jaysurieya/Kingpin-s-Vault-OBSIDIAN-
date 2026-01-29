LC - 200:
```
class Solution {
    public int numIslands(char[][] grid) {
        // optimal
        int count = 0;
        for(int i=0;i<grid.length;i++){
            for(int j=0;j<grid[0].length;j++){
                if(grid[i][j]=='1'){
                    dfs(grid,i,j);
                    count++;
                }
            }
        }
        return count;
    }
    public void dfs(char[][] grid,int i,int j){
        if(i<0 || j<0 || i>=grid.length || j>=grid[0].length || grid[i][j]=='0')  return;
        grid[i][j] = '0';
        dfs(grid,i-1,j);
        dfs(grid,i+1,j);
        dfs(grid,i,j-1);
        dfs(grid,i,j+1);
    }
}
```

LC - 695:
```
class Solution {
    public int maxAreaOfIsland(int[][] grid) {
        // optimal
        int count = 0;
        for(int i=0;i<grid.length;i++){
            int z =0;
            for(int j=0;j<grid[0].length;j++){
                if(grid[i][j]==1){
                    z = dfs(grid,i,j);
                    if(z>count) count=z;
                }
            }
        }
        return count;
    }
    public int dfs(int[][] grid,int i,int j){
        int c =0;
        if(i<0 || j<0 || i>=grid.length || j>=grid[0].length || grid[i][j]==0)  return 0;
        grid[i][j] = 0;
        c++;
        c+=dfs(grid,i-1,j);
        c+=dfs(grid,i+1,j);
        c+=dfs(grid,i,j-1);
        c+=dfs(grid,i,j+1);
        return c;
    }
}
```

LC - 419
```
class Solution {
    public int countBattleships(char[][] board) {
        // optimal
        int count = 0;
        for(int i=0;i<board.length;i++){
            for(int j=0;j<board[0].length;j++){
                if(board[i][j]=='X'){
                    dfs(board,i,j);
                    count++;
                }
            }
        }
        return count;
    }
    public void dfs(char[][] board,int i,int j){
        if(i<0 || j<0 || i>=board.length || j>=board[0].length || board[i][j]=='.')  return;
        board[i][j] = '.';
        dfs(board,i-1,j);
        dfs(board,i+1,j);
        dfs(board,i,j-1);
        dfs(board,i,j+1);
    }
}
```

LC - 2658
```
class Solution {
    public int findMaxFish(int[][] grid) {
        // optimal
        int count = 0;
        for(int i=0;i<grid.length;i++){
            int z =0;
            for(int j=0;j<grid[0].length;j++){
                if(grid[i][j]>0){
                    z = dfs(grid,i,j);
                    if(z>count) count=z;
                }
            }
        }
        return count;
    }
    public int dfs(int[][] grid,int i,int j){
        int c =0;
        if(i<0 || j<0 || i>=grid.length || j>=grid[0].length || grid[i][j]==0)  return 0;
        c+=grid[i][j];
        grid[i][j] = 0;
        c+=dfs(grid,i-1,j);
        c+=dfs(grid,i+1,j);
        c+=dfs(grid,i,j-1);
        c+=dfs(grid,i,j+1);
        return c;
    }
}
```

LC - 3619
```
class Solution {
    public int countIslands(int[][] grid, int k) {
        // optimal
        int count = 0;
        for(int i=0;i<grid.length;i++){
            long z =0;
            for(int j=0;j<grid[0].length;j++){
                if(grid[i][j]>0){
                    z = dfs(grid,i,j);
                    if(z%k==0) count++;
                }
            }
        }
        return count;
    }
    public long dfs(int[][] grid,int i,int j){
        long c =0;
        if(i<0 || j<0 || i>=grid.length || j>=grid[0].length || grid[i][j]==0)  return 0;
        c+=grid[i][j];
        grid[i][j] = 0;
        c+=dfs(grid,i-1,j);
        c+=dfs(grid,i+1,j);
        c+=dfs(grid,i,j-1);
        c+=dfs(grid,i,j+1);
        return c;
    }
}
```

LC - 1905
```

```
LC - 1992
```
class Solution {
    int r = 0,c = 0;
    public int[][] findFarmland(int[][] land) {
        List<int[]> res = new ArrayList<>();
        for(int i=0;i<land.length;i++){ 
            for(int j=0;j<land[0].length;j++){
                if(land[i][j]==1){
                    res.add(dfs(land,i,j));
                }
                r=0;
                c=0; 
            }
        }
        return res.toArray(new int[res.size()][]);
    }
    public int[] dfs(int[][] land,int i,int j){
        int arr[] = new int[4];
        arr[0] = i;
        arr[1] = j;
        dfs_1(land,i,j);
        arr[2] = r;
        arr[3] = c;
        return arr;
    }
    public void dfs_1(int[][] land,int i,int j){
        if(i<0 || j<0 || i>=land.length || j>=land[0].length || land[i][j]==0)  return ;
        if(i>r) r=i;
        if(j>c) c=j;
        land[i][j] = 0;
        dfs_1(land,i-1,j);
        dfs_1(land,i+1,j);
        dfs_1(land,i,j-1);
        dfs_1(land,i,j+1);
    }
}
```
LC - 463
LC - 130