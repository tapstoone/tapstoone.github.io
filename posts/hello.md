[category]: <> (General)
[date]: <> (2020/10/24)
[title]: <> (Hello world)

This is a blog post!

Here's some code:

```python
def hello():
    return 42

def generate_feed(global_config, metadatas):
    def get_link(route):
        return global_config['domain'] + "/" + route

    def get_date(date_text):
        year, month, day = (int(x) for x in date_text.split('/'))
        date = datetime.date(year, month, day)
        return date.strftime('%a, %d %b %Y 00:00:00 +0000')

    def get_item(metadata):
        return RSS_ITEM_TEMPLATE.format(
            title=metadata['title'],
            link=get_link('/'.join([global_config['posts_directory'], metadata['date'], metadata['filename']])),
            pub_date=get_date(metadata['date']), description=''
        )

    return RSS_MAIN_TEMPLATE.strip().format(
        title=global_config['title'],
        link=get_link(''),
        icon=global_config['icon'],
        items="\n".join(map(get_item, metadatas))
    )

```

``` typescript
import {
  Injectable,
  OnModuleInit,
  OnModuleDestroy,
  Logger,
} from '@nestjs/common';
import { PrismaClient } from '@prisma/client';

@Injectable()
export class PrismaService
  extends PrismaClient
  implements OnModuleInit, OnModuleDestroy
{
  private readonly logger = new Logger(PrismaService.name);
  private retryAttempts = 10;
  private retryDelay = 5000; // 5秒

  constructor() {
    super({
      log: ['error', 'warn'],
      datasources: {
        db: {
          url: process.env.DATABASE_URL,
        },
      },
      errorFormat: 'minimal',
    });

    this.$extends({
      query: {
        $allOperations: async ({ args, query }) => {
          try {
            return await query(args);
          } catch (error) {
            if (error.message.includes('Connection closed')) {
              this.logger.warn('数据库连接已关闭，尝试重新连接...');
              await this.reconnect();
              return query(args);
            }
            throw error;
          }
        },
      },
    });
  }

  async onModuleInit() {
    await this.connectWithRetry();
  }

  private async connectWithRetry(attempt = 1) {
    try {
      await this.$connect();
      this.logger.log('postgres数据库连接成功');

      this.$use(async (params, next) => {
        try {
          return await next(params);
        } catch (error) {
          if (error.message.includes('Connection closed')) {
            this.logger.warn('数据库连接已关闭，尝试重新连接...');
            await this.reconnect();
            return next(params);
          }
          throw error;
        }
      });
    } catch (error) {
      if (attempt <= this.retryAttempts) {
        this.logger.warn(
          `数据库连接失败，${this.retryDelay / 1000}秒后进行第 ${attempt} 次重试...`,
        );
        await new Promise((resolve) => setTimeout(resolve, this.retryDelay));
        return this.connectWithRetry(attempt + 1);
      }
      this.logger.error('数据库连接失败，已达到最大重试次数:', error);
      throw error;
    }
  }

```

```rust
impl AddLiquidity2 {
    pub fn instruction(
        &self,
        args: AddLiquidity2InstructionArgs,
    ) -> solana_program::instruction::Instruction {
        self.instruction_with_remaining_accounts(args, &[])
    }

    #[allow(clippy::arithmetic_side_effects)]
    #[allow(clippy::vec_init_then_push)]
    pub fn instruction_with_remaining_accounts(
        &self,
        args: AddLiquidity2InstructionArgs,
        remaining_accounts: &[solana_program::instruction::AccountMeta],
    ) -> solana_program::instruction::Instruction {
        let mut accounts = Vec::with_capacity(14 + remaining_accounts.len());
        accounts.push(solana_program::instruction::AccountMeta::new(
            self.position,
            false,
        ));
        accounts.push(solana_program::instruction::AccountMeta::new(
            self.lb_pair,
            false,
        ));
        if let Some(bin_array_bitmap_extension) = self.bin_array_bitmap_extension {
            accounts.push(solana_program::instruction::AccountMeta::new(
                bin_array_bitmap_extension,
                false,
            ));
        } else {
            accounts.push(solana_program::instruction::AccountMeta::new_readonly(
                crate::LB_CLMM_ID,
                false,
            ));
        }
        accounts.push(solana_program::instruction::AccountMeta::new(
            self.user_token_x,
            false,
        ));
        accounts.push(solana_program::instruction::AccountMeta::new(
            self.user_token_y,
            false,
        ));
        accounts.push(solana_program::instruction::AccountMeta::new(
            self.reserve_x,
            false,
        ));
        accounts.push(solana_program::instruction::AccountMeta::new(
            self.reserve_y,
            false,
        ));
        accounts.push(solana_program::instruction::AccountMeta::new_readonly(
            self.token_x_mint,
            false,
        ));
        accounts.push(solana_program::instruction::AccountMeta::new_readonly(
            self.token_y_mint,
            false,
        ));
        accounts.push(solana_program::instruction::AccountMeta::new_readonly(
            self.sender,
            true,
        ));
        accounts.push(solana_program::instruction::AccountMeta::new_readonly(
            self.token_x_program,
            false,
        ));
        accounts.push(solana_program::instruction::AccountMeta::new_readonly(
            self.token_y_program,
            false,
        ));
        accounts.push(solana_program::instruction::AccountMeta::new_readonly(
            self.event_authority,
            false,
        ));
        accounts.push(solana_program::instruction::AccountMeta::new_readonly(
            self.program,
            false,
        ));
        accounts.extend_from_slice(remaining_accounts);
        let mut data = borsh::to_vec(&AddLiquidity2InstructionData::new()).unwrap();
        let mut args = borsh::to_vec(&args).unwrap();
        data.append(&mut args);

        solana_program::instruction::Instruction {
            program_id: crate::LB_CLMM_ID,
            accounts,
            data,
        }
    }
}

#[derive(BorshSerialize, BorshDeserialize, Clone, Debug, Eq, PartialEq)]
#[cfg_attr(feature = "serde", derive(serde::Serialize, serde::Deserialize))]
pub struct AddLiquidity2InstructionData {
    discriminator: [u8; 8],
}

```

this is an image

![hello wordl](../../../../images/hello/hello-world.jpeg)