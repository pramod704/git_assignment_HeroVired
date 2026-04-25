# Git Assignment HeroVired

Repository: https://github.com/pramod704/git_assignment_HeroVired

## 1. Git user config
- `git config --global user.name "pramod704"`
- `git config --global user.email "pramodsingh3503@gmail.com"`

## 2. Branch setup and merges
- `git checkout -b dev`
- `git checkout -b main` (when missing) and push
- `git checkout -b feature/sqrt`
- `git checkout -b lfs`

## 3. Calculator improvements (feature/sqrt)
- Updated `Calculator.square_root`:
  - raise `ValueError` if x < 0
  - return `math.sqrt(x)` otherwise
- Updated `Calculator.divide` (dev):
  - raise `ValueError` if b == 0
  - return `a / b` otherwise

## 4. Git LFS branch
- Installed Git LFS
- `git lfs install`
- `git checkout -b lfs`
- created large file: `fsutil file createnew largefile.bin 220000000`
- `git lfs track "*.bin"`
- `git add .gitattributes largefile.bin`
- `git commit -m "Add Git LFS tracking for .bin files and large binary file"`
- `git push -u origin lfs`

## 5. Geometry calculator project
- Created `geometry-calculator` branch and `geometry_calculator.py`
- feature/circle-area: circle area implementation
- feature/rectangle-area: rectangle area implementation
- used `git stash` on each feature before commit
- merged both features into `dev`, resolved conflicts, then merged to `main`

## 6. Recommended manual pull request workflow
1. Go to https://github.com/pramod704/git_assignment_HeroVired
2. Create PR from `feature/circle-area` -> `dev` (or `main`)
3. Create PR from `feature/rectangle-area` -> `dev` (or `main`)
4. Review + approve + merge each PR
5. Optionally delete source branches after merge

## 7. Run code tests
- `python calculator.py` (requires Python installed and path set)
- `python geometry_calculator.py`

## 8. Submission
- Provide repo link in Vlearn: https://github.com/pramod704/git_assignment_HeroVired
- All branches pushed and features implemented
