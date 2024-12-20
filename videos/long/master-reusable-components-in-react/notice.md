# Tests pour le composant DataTable

## 1. Tests Unitaires

Les tests unitaires vérifient le comportement des composants individuels de manière isolée.

```tsx
import { render, screen } from '@testing-library/react';
import { TableCell } from '../DataTable';

describe('TableCell', () => {
  it("affiche la valeur en tant que chaîne de caractères quand render n'est pas fourni", () => {
    render(<TableCell value={42} />);
    expect(screen.getByText('42')).toBeInTheDocument();
  });

  it('utilise la fonction render personnalisée quand elle est fournie', () => {
    const customRender = (value: number) => <span>Numéro: {value}</span>;
    render(<TableCell value={42} render={customRender} />);
    expect(screen.getByText('Numéro: 42')).toBeInTheDocument();
  });
});
```

## 2. Tests d'Intégration

Les tests d'intégration vérifient l'interaction entre plusieurs composants.

```tsx
import { render, screen } from '@testing-library/react';
import DataTable from '../DataTable';

describe('DataTable', () => {
  const testData = [{ id: 1, name: 'Test User', email: 'test@example.com' }];

  const columns = [
    { key: 'name', header: 'Nom' },
    {
      key: 'email',
      header: 'Email',
      render: (value: string) => <a href={`mailto:${value}`}>{value}</a>,
    },
  ];

  it('rend correctement le header et les données', () => {
    render(<DataTable data={testData} columns={columns} />);

    // Vérifie les en-têtes
    expect(screen.getByText('Nom')).toBeInTheDocument();
    expect(screen.getByText('Email')).toBeInTheDocument();

    // Vérifie les données
    expect(screen.getByText('Test User')).toBeInTheDocument();
    expect(screen.getByRole('link')).toHaveAttribute(
      'href',
      'mailto:test@example.com',
    );
  });
});
```

## 3. Tests End-to-End (E2E)

Les tests E2E vérifient le fonctionnement complet de l'application en simulant les interactions utilisateur.

```typescript
describe('Employee Table', () => {
  beforeEach(() => {
    cy.visit('/');
  });

  it('affiche et interagit avec le tableau des employés', () => {
    // Vérifie le titre
    cy.get('h1').should('contain', 'Employee Table');

    // Vérifie la présence des colonnes
    cy.get('th').should('have.length', 4);

    // Vérifie les données des employés
    cy.get('tbody tr').should('have.length', 3);

    // Test d'interaction avec le lien email
    cy.get('a[href="mailto:john@example.com"]')
      .should('exist')
      .and('have.text', 'john@example.com');

    // Vérifie le style des badges de rôle
    cy.get('span').contains('Developer').should('have.class', 'bg-gray-100');
  });
});
```

## Configuration requise

Pour implémenter ces tests, vous devez installer les dépendances suivantes :

```bash
npm install --save-dev @testing-library/react jest cypress

```

## Structure de la pyramide de tests

La stratégie de test suit généralement une pyramide :

1. Base : Tests unitaires (nombreux et rapides)
2. Milieu : Tests d'intégration (moins nombreux)
3. Sommet : Tests E2E (peu nombreux mais plus complets)

## Points clés à tester

- Tests unitaires : Comportement individuel des composants (TableCell, TableHeader)
- Tests d'intégration : Interaction entre composants (DataTable avec ses sous-composants)
- Tests E2E : Flux complet de l'application (rendu du tableau, interactions utilisateur)
