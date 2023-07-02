The functions which are used in the code are found in the file [Useful functions](Useful_functions/Useful_functions.md).

```Python
map_img_g1g002_RAW = EQUIRECT_PROJ_raw(g1g002_raw)
fig, ax = plt.subplots(figsize=(20,12))
ax.text(270, 85, 'T R A I L I N G    H E M I S P H E R E', c='white', alpha=1, fontsize=12, ha='center', va='center')
ax.text(90, 85, 'L E A D I N G    H E M I S P H E R E', c='white', alpha=1, fontsize=12, ha='center', va='center')
EQUIRECT_PLOT(ax, map_img_g1g002_RAW)
```
<img width="997" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/23d08584-ccc0-42c9-8c48-2628a6fe6f6b">

```Python
# """ Useful mask """
for_mask1 = map_img_g1g002_RAW.copy()
for_mask1[ for_mask1 > 0.] = 0.
MASK1 = np.isnan(for_mask1[0])
fig, ax = plt.subplots(figsize=(20,8))
Ganymede_plot(ax)
ax.imshow(~MASK1, cmap=cm.gray, extent=(0,360,90,-90), alpha=0.2)
ax.text(270, 85, 'T R A I L I N G    H E M I S P H E R E', c='white', alpha=0.75, fontsize=12, ha='center', va='center')
ax.text(90, 85, 'L E A D I N G    H E M I S P H E R E', c='white', alpha=0.75, fontsize=12, ha='center', va='center')
#ax.grid(alpha=0.25)
plt.show()
```
<img width="789" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/081fe326-678c-4146-a390-835120a06d1b">

```Python
map_img_g1g002 = EQUIRECT_PROJ(g1g002_MASK_ie)
fig, ax = plt.subplots(figsize=(20,8))
EQUIRECT_PLOT(ax, map_img_g1g002)
```
<img width="689" alt="image" src="https://github.com/gransss/Master_thesis/assets/136255551/8256a7cd-ffa0-4343-9057-c9b4b536bd42">

```Python
# """ Useful mask """
for_mask = map_img_g1g002.copy()
for_mask[ for_mask > 0.] = 0.
MASK = np.isnan(for_mask[0])

fig, ax = plt.subplots(figsize=(18,9))
Ganymede_plot(ax)
ax.imshow(~MASK, cmap=cm.gray, extent=(0,360,90,-90), alpha=0.4)
ax.text(270, 85, 'T R A I L I N G    H E M I S P H E R E', c='white', alpha=0.5, fontsize=12, ha='center', va='center')
ax.text(90, 85, 'L E A D I N G    H E M I S P H E R E', c='white', alpha=0.5, fontsize=12, ha='center', va='center')
#ax.grid(alpha=0.25)
plt.show()
```
<img width="880" alt="Screenshot 2023-07-02 alle 18 10 58" src="https://github.com/gransss/Master_thesis/assets/136255551/b23598ad-b50c-437b-8b3f-5e9b26d5e3ac">


