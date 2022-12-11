<main>
  <div class="mdc-typography--headline2">Lambda+DynamoDB Client</div>

  <DataTable table$aria-label="Item list" style="width: 100%;">
    <Head>
      <Row>
        <Cell numeric>ID</Cell>
        <Cell style="width: 100%;">Name</Cell>
        <Cell numeric>Price</Cell>
      </Row>
    </Head>
    <Body>
    {#each items as item}
      <Row>
        <Cell>{item.id}</Cell>
        <Cell>{item.name}</Cell>
        <Cell numeric>{item.price}</Cell>
      </Row>
    {/each}
    </Body>
    <LinearProgress
      indeterminate
      bind:closed={loaded}
      aria-label="Data is being loaded..."
      slot="progress"
    />
  </DataTable>
  <BottomAppBar bind:this={bottomAppBar}>
    <Section>
    </Section>
    <Section fabInset>
      <Fab aria-label="New item" on:click={() => (open = true)}>
        <Icon class="material-icons">add</Icon>
      </Fab>
    </Section>
    <Section>
    </Section>
  </BottomAppBar>
</main>

<Dialog bind:open sheet aria-describedby="sheet-content">
  <Title id="simple-title">Add Item</Title>
  <Content id="sheet-content">
    <IconButton action="close" class="material-icons">close</IconButton>
    <Textfield variant="outlined" bind:value={id} label="ID">
      <HelperText slot="helper">Item ID</HelperText>
    </Textfield>
    <Textfield variant="outlined" bind:value={name} label="Name">
      <HelperText slot="helper">Item Name</HelperText>
    </Textfield>
    <Textfield variant="outlined" bind:value={price} label="Price">
      <HelperText slot="helper">Item Price</HelperText>
    </Textfield>
    <Actions>
      <Button on:click={() => onclick()}>
        <Label>Send</Label>
      </Button>
    </Actions>
  </Content>
</Dialog>


<script lang="ts">
  import IconButton from '@smui/icon-button';

  import DataTable, { Head, Body, Row, Cell } from '@smui/data-table';
  import LinearProgress from '@smui/linear-progress';

  import BottomAppBar, {
    Section,
  } from '@smui-extra/bottom-app-bar';
  import Fab, { Icon } from '@smui/fab';
  let bottomAppBar: BottomAppBar;

  import Dialog, { Title, Content, Actions } from '@smui/dialog';
  import Textfield from '@smui/textfield';
  import HelperText from '@smui/textfield/helper-text';
  import Button, { Label } from '@smui/button';
  let open = false

  let id = '';
  let name = '';
  let price = '';

  type Item = {
    id: string,
    name: string,
    price: string
  }

  let items: Item[] = [];
  let loaded:boolean;

  const baseUrl = 'https://ocvwt1izg4.execute-api.ap-northeast-1.amazonaws.com'
  async function fetchItems() {
    loaded = false;
    try {
      const res = await fetch(
        baseUrl + '/items',
        {
          method: 'GET'
        }
      );
      const res_json = await res.json();
      items = res_json["Items"];
    } catch (error) {
      console.log(error);
    } finally {
      loaded = true;
    }
  }

  fetchItems()

  async function addItem() {
    try {
      const res = await fetch(
        baseUrl + '/items',
        {
          method: 'PUT',
          headers: {
            "Content-Type": "application/json"
          },
          body: JSON.stringify({
            "id": id,
            "name": name,
            "price": price
          })
        }
      );
      console.log(res);
    }catch (error) {
      console.log(error);
    }
  }

  async function onclick() {
    await addItem();
    await fetchItems()
  }

</script>
