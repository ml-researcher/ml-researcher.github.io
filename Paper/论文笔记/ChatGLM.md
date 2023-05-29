## tokenizer

x x x x 130001(gmask) 130004(bos) x x x x

## attention mask

gmask左边（包括gmask）都是full attention

bos开始是下三角

## position id

position_ids      : 0, 1, 2, 3(gmask), 3(bos), 3, 3, 3
block_position_ids: 0, 0, 0, 0(gmask), 1(bos), 2, 3, 4