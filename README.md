# Use the PowerShell tree function

This is the most effective and recommended solution, as it prevents the expensive recursion into the unwanted directories entirely. You can add this custom function to your PowerShell session or profile for future use. 

## Step 1: Create the function
Copy and paste this code into your PowerShell terminal and press Enter. 

```ps
function Get-Tree {
    param (
        [string]$Path = '.',
        [int]$Depth = 99,
        [string[]]$Exclude = @('node_modules', 'cache')
    )
    $indent = 0
    function Get-ChildTree {
        param ($Path, $indent)
        $items = Get-ChildItem -Path $Path -Force -Exclude $Exclude
        $count = $items.Count
        $i = 1
        foreach ($item in $items) {
            $isLast = ($i -eq $count)
            $prefix = "│   " * $indent
            if ($isLast) {
                $prefix = $prefix + "└── "
            } else {
                $prefix = $prefix + "├── "
            }
            Write-Host "$prefix$($item.Name)"
            if ($item.PSIsContainer -and ($indent + 1) -lt $Depth) {
                Get-ChildTree -Path $item.FullName -indent ($indent + 1)
            }
            $i++
        }
    }
    Write-Host (Get-Item $Path).Name
    Get-ChildTree -Path $Path -indent $indent
}
```

## Step 2: Run the function

You can now simply run Get-Tree from your project's root directory. It will generate a proper tree output that respects your exclusion list.

```ps
Get-Tree
```

To exclude additional folders, just pass them to the -Exclude parameter.

```ps
Get-Tree -Exclude 'node_modules', 'cache', 'dist'
```
